---
title: "Logging Performance Stats to SQLite with ChatGPT"
datePublished: Fri Mar 17 2023 13:14:57 GMT+0000 (Coordinated Universal Time)
cuid: clfckbclt000109jsf2ypejox
slug: logging-performance-stats-to-sqlite-with-chatgpt
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ztYmIQecyH4/upload/2867fb9ffd9436444657e71ae3466c5f.jpeg
tags: sqlite, powershell, chatgpt

---

Time for another pair-programming session with [ChatGPT](https://openai.com/blog/chatgpt).

> Can you write a powershell script to read the computer's performance statistics every 5 seconds and dump the results to a sqlite file?

The eager-to-please chatbot instantly wrote out a solution.

But it didn't work.

The problem seemed to be that it had sourced its [SQLite](https://sqlite.org/)\-interacting commands from some miscellaneous place, and it didn't go well when I tried to track them down and install them.

I found another SQLite PowerShell command on GitHub, but the chatbot fell short when I asked it to use it.

## Changing tactics

As my goal was a quick solution and not any particular implementation, I tried a different tactic:

> Can you write a powershell script to read the computer's performance statistics every 5 seconds and dump the results to a sqlite file **via the sqlite command line interface**?

The result was a complete success:

```powershell
# Set the path to the SQLite command-line interface
$sqlitePath = "C:\sqlite\sqlite3.exe"

# Set the path to the SQLite database file
$databasePath = "C:\sqlite\performance_stats.db"

# Create a table to hold the performance statistics
$tableCreationQuery = @"
CREATE TABLE IF NOT EXISTS performance_stats (
    timestamp INTEGER,
    cpu_usage REAL,
    memory_usage INTEGER
);
"@

# Execute the table creation query using the SQLite command-line interface
& $sqlitePath $databasePath "$tableCreationQuery"

# Loop indefinitely, reading performance statistics and writing them to the database
while ($true) {
    # Get the current timestamp and performance statistics
    $timestamp = [int]([DateTime]::UtcNow - [DateTime]::ParseExact("01/01/1970", "MM/dd/yyyy", $null)).TotalSeconds
    $cpuUsage = Get-Counter -Counter "\Processor(_Total)\% Processor Time" | Select-Object -ExpandProperty CounterSamples | Select-Object -ExpandProperty CookedValue
    $memoryUsage = Get-Counter -Counter "\Memory\Available MBytes" | Select-Object -ExpandProperty CounterSamples | Select-Object -ExpandProperty CookedValue

    # Build the query to insert the performance statistics into the database
    $insertQuery = "INSERT INTO performance_stats (timestamp, cpu_usage, memory_usage) VALUES ($timestamp, $cpuUsage, $memoryUsage);"

    # Execute the insert query using the SQLite command-line interface
    & $sqlitePath $databasePath "$insertQuery"

    # Wait for 5 seconds before collecting the next set of performance statistics
    Start-Sleep -Seconds 5
}
```

## ChatGPT, read those logs back to me!

After running the above script, I had a nice database full of log entries.

Now I want to quickly inspect those entries:

> Can you write a second powershell script, also using the powershell CLI, which simply runs and prints the contents of the table in a human-readable format?

## Another little stumble

It seems the chatbot is often, so close, but not quite there. Running its code caused only one line of the logs to be written to the console.

> The code you wrote only prints one row even though there are many in the table

It doesn't even get offended when you point out its mistakes:

> I apologize for the error in my previous response. You are correct that the code I provided only prints one row even though there are many in the table. Here's an updated script that should print all rows in the table:

## Success (ish)!

```powershell
# Set the path to the SQLite command-line interface
$sqlitePath = "C:\sqlite\sqlite3.exe"

# Set the path to the SQLite database file
$databasePath = "C:\sqlite\performance_stats.db"

# Build the query to select all rows from the performance_stats table
$selectQuery = "SELECT * FROM performance_stats;"

# Execute the select query using the SQLite command-line interface and capture the output
$output = & $sqlitePath $databasePath "$selectQuery" | Out-String

# Split the output into individual rows
$rows = $output -split "`n"

# Print each row in a human-readable format
foreach ($row in $rows) {
    if ($row -ne "") {
        $fields = $row -split '\|' | ForEach-Object { $_.Trim() }
        Write-Host ("Timestamp: {0}, CPU Usage: {1:F2}%, Memory Usage: {2} MB" -f [DateTime]::ParseExact("01/01/1970", "MM/dd/yyyy", $null).AddSeconds($fields[0]), $fields[1], $fields[2])
    }
}
```

Which outputs something like this:

```plaintext
Timestamp: 15/03/2023 23:05:50, CPU Usage: 2.21654420080408%, Memory Usage: 2134 MB
Timestamp: 15/03/2023 23:05:57, CPU Usage: 0.0%, Memory Usage: 2140 MB
Timestamp: 15/03/2023 23:06:04, CPU Usage: 6.65153110991866%, Memory Usage: 2128 MB
Timestamp: 15/03/2023 23:06:11, CPU Usage: 0.0%, Memory Usage: 2131 MB
Timestamp: 15/03/2023 23:06:18, CPU Usage: 8.23848719477404%, Memory Usage: 2082 MB
Timestamp: 15/03/2023 23:06:25, CPU Usage: 7.30237538288964%, Memory Usage: 2088 MB
Timestamp: 15/03/2023 23:06:32, CPU Usage: 5.51703670288382%, Memory Usage: 2141 MB
Timestamp: 15/03/2023 23:06:39, CPU Usage: 3.22791711015336%, Memory Usage: 2161 MB
Timestamp: 15/03/2023 23:06:46, CPU Usage: 3.5388217767915%, Memory Usage: 2151 MB
Timestamp: 15/03/2023 23:06:53, CPU Usage: 1.63911487875544%, Memory Usage: 2125 MB
```

## The devil is in the details

As impressive as this all is, the last time my memory usage was ever as low as 2GB was many years before ChatGPT was born.

The script shows the *available* memory, but I wanted to see how much memory is *in use*.

So beware that ChatGPT can help with tasks like this, but you'll still need to fact-check, sanity-check, nudge and correct the nitty-gritty of the code it produces.

## One last snazzy thing

Me:

> Could you combine these into a single script, which prompts the user to enter the sqlitePath, the databasePath and to say if they want to RECORD or VIEW?

ChatGPT:

> Sure! Here's an example PowerShell script that prompts the user to enter the path to the SQLite command-line interface, the path to the SQLite database file, and whether they want to record performance statistics or view the contents of the database:

And after my little intervention to pick the right memory statistic, the script looks like this:

%[https://gist.github.com/stevespringer/c4e8a79220712ab2cd97bc7f6cfce313]