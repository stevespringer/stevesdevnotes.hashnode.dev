---
title: "Exploring ChatGPT with MIDI files"
datePublished: Wed Mar 15 2023 16:21:39 GMT+0000 (Coordinated Universal Time)
cuid: clf9w3rbr000209lacyzmcwf7
slug: exploring-chatgpt-with-midi-files
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/AQIB4qsI8wI/upload/eb1661c17e2a0e7498bdae5a8f91f6b1.jpeg
tags: music, chatgpt

---

Yesterday, I decided to try out [ChatGPT](https://openai.com/blog/chatgpt) for the first time. What could I ask it to do? How about:

> Can you write me a C# program which finds the most common pitch in a midi file?

Amazingly, after some friendly conversational preamble, it churned out the following code, finishing with a paragraph instructing me where to put the path to my [MIDI file](https://en.wikipedia.org/wiki/MIDI) and then explaining what the code would do.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace MidiPitchAnalyzer
{
    class Program
    {
        static void Main(string[] args)
        {
            // Set the path to the MIDI file you want to analyze
            string filePath = "path/to/midi/file.mid";

            // Read the MIDI file into a byte array
            byte[] midiData = File.ReadAllBytes(filePath);

            // Find all the MIDI note events in the file
            List<byte> noteEvents = new List<byte>();
            for (int i = 0; i < midiData.Length - 2; i++)
            {
                if (midiData[i] == 0x90) // MIDI note on event
                {
                    byte pitch = midiData[i + 1];
                    noteEvents.Add(pitch);
                }
            }

            // Find the most common pitch in the MIDI file
            byte mostCommonPitch = noteEvents
                .GroupBy(x => x)
                .OrderByDescending(x => x.Count())
                .First()
                .Key;

            // Output the most common pitch to the console
            Console.WriteLine($"The most common pitch in the MIDI file is {mostCommonPitch}.");
        }
    }
}
```

## My first impressions

The positives:

* It compiles.
    
* It seems to address my requirement, which I had stated in natural language.
    
* It defined a namespace with a sensible and clear name: `MidiPitchAnalyzer.`
    
* There's a decent number of well-written comments at good points throughout the code.
    
* Without running anything, the code *looks* logical. Stylistically, it seems sound, in terms of its formatting, spacing, variable definitions and naming.
    

The negatives:

* The answer it produced was wrong (more below).
    
* The output is a Midi note number (59). I would have preferred the pitch name (B3). Perhaps I should have worded my question more precisely.
    

Other observations:

* Performance: a forward-read of the file, counting occurrences in a dictionary would have sufficed.
    
* I'd prefer a named constant to the inline magic byte literal`0x90`.
    
* The same question won't always produce the same answer on ChatGPT. I copy-pasted the question into a new chat and it produced code that took quite a different approach.
    

## Attempt 2 - Asking it to take a different approach

I chose the MIDI file format for this experiment because I know nothing about its internal details. A Google search suggests that the approach above is missing out on lots of the detail contained in the MIDI file, for example, notes contained in different channels or instruments.

So let's try some more precise instructions:

> Please use C# and DryWetMIDI to print all of the note names and the count of their occurances in a midi file, ordered by most to least commonly occuring

ChatGPT then composed a vastly superior (and now correct) example program:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Melanchall.DryWetMidi.Core;

class Program
{
    static void Main(string[] args)
    {
        if (args.Length != 1)
        {
            Console.WriteLine("Usage: MidiNoteCounter <midi file>");
            return;
        }

        var midiFile = MidiFile.Read(args[0]);

        var noteCounts = new Dictionary<string, int>();

        foreach (var note in midiFile.GetNotes())
        {
            var noteName = note.NoteName;

            if (!noteCounts.ContainsKey(noteName))
            {
                noteCounts[noteName] = 0;
            }

            noteCounts[noteName]++;
        }

        var sortedNoteCounts = noteCounts.OrderByDescending(pair => pair.Value);

        foreach (var pair in sortedNoteCounts)
        {
            Console.WriteLine("{0}: {1}", pair.Key, pair.Value);
        }
    }
}
```

As a bonus, it takes the MIDI file as a command-line argument, validates that the argument has been supplied, and even outputs usage guidance if the program is run without the argument. It might not be production-ready code, but is pretty neat nonetheless!

The coding style of this second routine is noticeably different:

* There are no comments.
    
* No namespace was defined.
    
* Implicit typing (the `var` keyword) is used everywhere, whereas typing was entirely explicit in the first example.
    

## Scope creep

To see if ChatGPT could handle feature requests on the code it's written, I asked it:

> Are you able to make it so it groups by note name and octave?

And it did it perfectly! It helpfully explained its code change in the following paragraph:

> In this version of the code, we concatenate the note name and octave together to form the key in the \`**noteCounts**\` dictionary, and then we sort the dictionary based on the value of the counts, just like before. This will group the note names by octave and then by note name, so that you get a count of how many times each note name and octave combination appears in the MIDI file.

%[https://gist.github.com/stevespringer/0f1896a6b93ba54ce2f3e6639714bff0] 

## Initial reflections

I've seen people dismiss ChatGPT because it doesn't "understand" what it's doing. But as long as you, the user, understand what you are doing, you could benefit from applying this tool to the right problems.

You should, however, of course, scrutinise the code it produces, suspicious of any nonsense and noise that might make their way through.

In response to alarming headlines about this tech threatening human jobs, it's easy to point attention to its shortcomings and to list all the human activity that the ChatGPT can't replicate.

But if you turn your imagination and curiosity to what this tool *is* capable of helping with, you could benefit from delegating the authoring of specific routines to ChatGPT, subject to human review and tweaks.

## Knowing the limitations of a tool

To caveat this optimism, be aware that ChatGPT is not likely to give up-to-the-minute accurate answers, as it generates its responses using historical data frozen at a point in time.

So, if it happens to be important in the code you're writing, check that there's not some aspect of the stack or libraries that you're using which requires a newer implementation, or could benefit from more recent features.

## In conclusion

Don't over-hype it, but also don't dismiss it: ChatGPT could help you to more quickly concoct simple tools, utilities, boilerplate code, and "first drafts" of routines that you'll later finess yourself. Crafting your requests to be clear and precise may help nudge the chatbot in the right direction, and you can even iterate over your implementations by asking it to make specific changes.