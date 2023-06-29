Every now and then you get a rather large dataset, with many files. In Computer Vision world, it is common to have these organised by classes.
For example, I have a dataset of images of cars. It looks a bit like this:

```
$ tree .
data.csv
IMAGES
  Alfa
    - 10.jpg
    - 11.jpg
    - 12.jpg
    -...
  BMW
    - 20.jpg
    - 21.jpg
    - 22.jpg
    -...
  Chrysler
  ...
```

One canonical way to count files in a directory is this magic incantation: `find . -type f | wc -l`. Here's the breakdown for it:

- `find` - a Unix program to search files and folders on disk
- `.` - the directory to be searched. As usual in UNIX, `.` means "current directory"
- `-type` - a predicate or filter for `find`. In this case, we want to filter by an "entry"'s type.
- `f` - the argument for the predicate. `f` means _file_. We could have also passed `d` if we wanted to search for directories
- `|` - a UNIX pipe. It means "take the output of the previous command, and feed it as input to the next command". After this symbol, we can start typing the next command to process our output
- `wc` - no, it's not a toilet, it's short for _*w*ord *c*ount_. A simple UNIX utility to count stuff in input
- `-l` - a flag for `wc`, meaning we are only interested in the _*l*ines_. So count how many lines are there in input

As you can imagine, this does a full traversal of the path, asking the operating system: _Hey, this thing called "Alfa", is it a file?_ . If the OS says "yes", then it prints the name. Otherwise, it goes to the next one. _Hey, this thing called "Alfa/10.jpg", is it a file?_ . And this time, OS says _Yes_, so the name is printed on a line.

If you have millions of files, this is going to be VERY slow.

Is there a faster way?

Yes, there is. We can take advantage of the fact that each directory "knows" which files are inside of it. This information is stored in the filesystem. And rather than being scattered all across the disk, as the files may be, it is all compact in the filesystem table. How can we access this information easily ?

We can take advantage of the rather flat directory structure together with "globbing". We ask the shell "what are all the files in this directory?". And then count that. This doesn't need to iterate through each file, it exploits the filesystem to show the information.

So the command becomes: `ls IMAGES/* | wc -l`. The `*` is a "glob", expanded by the shell to all the directories in `IMAGES`. So the command that is being run becomes `ls IMAGES/Alpha Images/BMW Images/Chrysler`. For each argument, `ls` will then print its files. And just like before, we feed that output to the counting command.

If you do this, you will notice a small difference in count between the first and 2nd command. Until I get access to my blog from the work computer to add an example, I let you investigate where this difference is coming from.
