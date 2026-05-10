# Print code on paper

Use the utility [enscript](https://www.gnu.org/software/enscript/).

Example of usage

    enscript -1rG --line-numbers -p out.ps --highlight=python -c inputfile.py

Where the flag `-1rG` means:

* `-1 -2 -3 -4` number of columns per page;
* `-r` rotate (landscape mode);
* `-G` fancy header (with filename, date and time).

The other flags are self explanatory, to view all of them run

    enscript --help
