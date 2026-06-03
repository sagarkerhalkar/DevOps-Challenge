 Linux File I/O Practice Log
> Day 05 - 90DaysOfDevOps Challenge  
> Hands-on practice with fundamental text stream redirection, file creation, and partial file reading.

---

 📝 File I/O Command Execution Flow

This log details the exact steps executed to create, manipulate, and inspect a local text file using core Linux utilities.

 1. File Creation & Initialization
   Command: touch notes.txt  
       Description: Creates an empty file named notes.txt in the current working directory without modifying existing contents if the file already exists.
       Result: Verified empty file initialization via ls -l.

 2. Overwriting Text (Standard Redirection)
   Command: echo "Line 1: Learning DevOps fundamentals with TrainWithShubham." > notes.txt  
       Description: Uses the > operator to redirect stdout to notes.txt. This completely overwrites any previous content in the target file.

 3. Appending Text
   Command: echo "Line 2: Today is all about mastering Linux text redirection streams." >> notes.txt  
       Description: Uses the >> operator to append the string to the end of notes.txt without disturbing existing lines.

 4. Concurrent Writing and Display (The Pipeline)
   Command: echo "Line 3: Writing automation scripts depends heavily on these file basics." | tee -a notes.txt  
       Description: Taps the data stream using tee. The -a flag appends the input to notes.txt while simultaneously spitting it out to stdout.
       Console Output:  
text
        Line 3: Writing automation scripts depends heavily on these file basics.
        

---

  Reading Back File Outputs

 5. Viewing Full Content
   Command: cat notes.txt  
       Description: Concatenates and displays the entire content of notes.txt.
       Console Output:  
text
        Line 1: Learning DevOps fundamentals with TrainWithShubham.
        Line 2: Today is all about mastering Linux text redirection streams.
        Line 3: Writing automation scripts depends heavily on these file basics.
        

 6. Inspecting the Head (Top Lines)
   Command: head -n 2 notes.txt  
       Description: Reads and displays only the first 2 lines from the top of the file.
       Console Output:  
text
        Line 1: Learning DevOps fundamentals with TrainWithShubham.
        Line 2: Today is all about mastering Linux text redirection streams.
        

 7. Inspecting the Tail (Bottom Lines)
   Command: tail -n 2 notes.txt  
       Description: Reads and displays only the last 2 lines from the bottom of the file.
       Console Output:  
text
        Line 2: Today is all about mastering Linux text redirection streams.
        Line 3: Writing automation scripts depends heavily on these file basics.
        

---

 💡 Key Takeaway for DevOps
Configuration files, environment variables, system logs, and automation scripts are fundamentally just text files. Knowing how to manipulate streams dynamically at the CLI without relying on heavy graphical editors is a non-negotiable superpower when automating pipelines or tweaking configurations over remote SSH connections.