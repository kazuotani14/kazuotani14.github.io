# Linux utilities

[Bash command line shortcuts](http://teohm.com/blog/shortcuts-to-move-faster-in-bash-command-line/)

[TLDR pages](http://tldr.sh/#installation)

* `ctrl + a` : move to front of line
* `ctrl + e` : move to end of line
* `ctrl + l` : clear screen

* parentheses and && for multiple lines of commands in terminal

* https://en.m.wikibooks.org/wiki/Bash_Shell_Scripting
* https://quickleft.com/blog/command-line-tutorials-sed-awk/
* http://www.theunixschool.com/p/awk-sed.html
* http://www-users.york.ac.uk/~mijp1/teaching/2nd_year_Comp_Lab/guides/grep_awk_sed.pdf

* `mdls [filename]` to see metadata

* `sudo updatedb` -> `locate [filename]` to find files

* [chmod](https://www.computerhope.com/unix/uchmod.htm)
    * `chmod u=rwx,g=rw,o=r myfile` is equivalent to `chmod 754 myfile`
        * user, group, others
        * 4: read, 2: write, 1: execute, 0: no permission

* `wget [http url]` to download file
    - `curl` works too

* [Checking filesizes](https://stackoverflow.com/questions/11720079/how-can-i-see-the-size-of-files-and-directories-in-linux)
    * `ls -l filename`
    * `du -sh directory_name`

* CMU SSH: `ssh kotani@unix.andrew.cmu.edu`
    - `-p [number]` to designate port
* SCP for copying files from remote to local `[from] [to]`: `scp kotani@unix.andrew.cmu.edu:/afs/andrew.cmu.edu/usr1/kotani/[file] [local path]`


* [compression tools](https://www.digitalocean.com/community/tutorials/an-introduction-to-file-compression-tools-on-linux-servers)
* `tar -xzf [file].tar.gz`
	* x: extract, z: gzip, f: file path
* `tar -czf archive.tar.gz stuff` : compress
    - `stuff` can be multiple directories/files
    - `--exclude=*.mp4`
- `unzip` also works

* To see assembly: `objdump -d a.out`, or `gdb a.out`, `disassemble [function name]`, or `gcc -Og -S`

### Search, file manipulation

* grep: operates on lines of text
    * `grep -nr phrase .` to search for phrase in directory
        * `-n` is to show line numbers
        * `-r` for recursive search
    * `--include="*.cpp"` to only search files of one type
    * `egrep` == `grep -E` : prints all lines that match a certain pattern
        - `-E` for extended regular expression, which lets you get away with not using slashes before special characters: <http://www.panix.com/~elflord/unix/grep.html>
    - `-l` : only print filename of files with matching lines
    - `-i` : ignore lower/upper cases

* [piping and redirecting](https://ryanstutorials.net/linuxtutorial/piping.php)
    - Redirecting: sending data from program to text file
        - `command > data.txt` to redirect stdout of program to file
            - `>>` : append
            - `wc -l command > data.txt` : clear file before writing
    - Piping: sending data from program to another program
        - e.g. `find . -print | grep -i foo` - send results of `find . -print` (all files found recursively in current directory) to be input of grep

* find: operates on filenames
    * `find . -name "filename"`, optional `*` in filename
    * Piping find into grep is often more convenient; it gives you the full power of regular expressions for arbitrary wildcard matching.
        * To find all files with "foo" in filename: `find . -print | grep -i foo`

* awk: For each line of the input file, it sees if there are any pattern-matching instructions, in which case it only operates on lines that match that pattern, otherwise it operates on all lines.

* sed: stream editor
    - <http://www-users.york.ac.uk/~mijp1/teaching/2nd_year_Comp_Lab/guides/grep_awk_sed.pdf>
    * Find and replace strings: `find /path/to/files -type f -exec sed -i "" "s/oldstring/new string/g" {} \;`

* Merging PDFs on OS X: http://gotofritz.net/blog/howto/joining-pdf-files-in-os-x-from-the-command-line/

### Networking

* Wireshark for packet analysis
    - https://www.howtogeek.com/104278/how-to-use-wireshark-to-capture-filter-and-inspect-packets/
    - https://cs.gmu.edu/~astavrou/courses/ISA_674_F12/Wireshark-Tutorial.pdf

* `ifconfig` : see IP addresses
* `nslookup` : find the IP address from hostname or vice-versa

### Add-ons

* Pdfgrep
	* To search a bunch of PDFs for pattern: `find . -iname '*.pdf' -exec pdfgrep "pattern" {} +`

* Htop
    * [Explanation of display](https://peteris.rocks/blog/htop/)
    * [in-depth guide](https://peteris.rocks/blog/htop/)
        * `shift+H` to toggle thread visibility
        * `F5` to see tree view of process

* Pdfcrop - remove white space around pdf documents
    * <http://alexsleat.co.uk/2011/01/25/using-pdfcrop-to-remove-white-margins-ubuntu/>
    * `pdfcrop input.pdf output.pdf`
    * Need texlive-extra-utils installed

* Markdown to PDF with pandoc: `pandoc -o [output].pdf [input].md`

* [google style guide cpplint](https://github.com/google/styleguide/tree/gh-pages/cpplint)
	* `chmod +x cpplint.py`
	* `./cpplint.py [file]`, `./cpplint.py --help`
* clang-format
	* `clang-format -i -style=Google [files]`
		* `-i` : inplace edits
		* Use `-style=file` to load style configuration from .clang-format file located in one of the parent directories of the source file (or current directory for stdin).

* Checking cpu temp and fan speed
    - os x: `gem install iStats`, `istats`
    - ubuntu: `sudo apt-get install lm-sensors`
        - `sudo sensors-detect`
        - `sudo service kmod start`
        - `sensors`

# OS X utilities

* iTerm
    - `cmd + d`, `cmd + shift + d` : split pane
    - `cmd + option + [arrow]` : switch panes
    - `cmd + t` : make new tab
    - `cmd + shift + enter` : expand/un-expand pane
    * https://webstudiya.com/develop-faster-with-iterm-profiles-and-window-arrangements/


* [wifi-password](https://github.com/rauchg/wifi-password)