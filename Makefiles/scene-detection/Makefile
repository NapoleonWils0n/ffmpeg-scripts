# ffmpeg scene detection makefile

# Move this Makefile into the same directory as the video to process

# you need to tell the Makefile the name of the video to process
# either by editing the Makefile and changing input.mp4 to the name of the video to process
# and then running: make

# or by running: make input=filename.mp4
# where filename.mp4 is the name of the file to process

# make clean: will prompt you to remove text files and video clips, except the input file


# change input.mp4 to the file name to process
input := input.mp4

# all - run the cut recipe which runs scene-detect, scene-time and scene-cut in order
all: cut

# scene-detect - input is the prerequisite
detect: $(input)
	@scene-detect -i $(input) -o detection.txt


# scene-time - detect is the prerequisite
time: detect
	@scene-time -i detection.txt -o cutlist.txt


# scene-cut - time is the prerequisite
cut: time
	@scene-cut -i $(input) -c cutlist.txt


# make clean - prompt to remove text files and video clips, except the input file
.PHONY: clean
clean:
	rm -fI cutlist.txt detection.txt $(wildcard *-[0-9]*.mp4)
