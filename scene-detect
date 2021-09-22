#!/bin/sh

#===============================================================================
# ffmpeg scene detection
#===============================================================================


#===============================================================================
# script usage
#===============================================================================

usage () {
# if argument passed to function echo it
[ -z "${1}" ] || echo "! ${1}"
# display help
echo "\
$(basename "$0") -s 00:00:00 -i infile -e 00:00:00 -t (0.1 - 0.9) -f sec -o outfile

-s 00:00:00 : start time
-i input.(mp4|mov|mkv|m4v)
-e 00:00:00 : end time
-t (0.1 - 0.9) # threshold
-f sec # output in seconds
-o output.txt"
exit 2
}


#===============================================================================
# error messages
#===============================================================================

INVALID_OPT_ERR='Invalid option:'
REQ_ARG_ERR='requires an argument'
WRONG_ARGS_ERR='wrong number of arguments passed to script'
NOT_MEDIA_FILE_ERR='is not a media file'
NOTFILE_ERR='not a file'


#===============================================================================
# check number of aruments passed to script
#===============================================================================

[ $# -gt 0 ] || usage "${WRONG_ARGS_ERR}"


#===============================================================================
# regular expressions for the expr command
#===============================================================================

# timecode - match 00:00:00
timecode='^[0-9]\{2\}:[0-9]\{2\}:[0-9]\{2\}$'

# threshold_regex - match 0.1-9
threshold_regex='^[0]\{1\}[.]\{1\}[1-9]\{1\}$'

# format_regex seconds
format_regex='^sec$'

#===============================================================================
# getopts check options passed to script
#===============================================================================

while getopts ':s:i:e:t:o:f:h' opt
do
  case ${opt} in
     s) start="${OPTARG}"
         expr "${start}" : "${timecode}" 1>/dev/null || usage;;
     i) input="${OPTARG}"
	[ -f "${input}" ] || usage "${input} ${NOTFILE_ERR}";;
     e) end="${OPTARG}"
        expr "${end}" : "${timecode}" 1>/dev/null || usage;;
     t) threshold="${OPTARG}"
        expr "${threshold}" : "${threshold_regex}" 1>/dev/null || usage;;
     o) output="${OPTARG}";;
     f) format="${OPTARG}"
        expr "${format}" : "${format_regex}" 1>/dev/null || usage;;
     h) usage;;
     \?) usage "${INVALID_OPT_ERR} ${OPTARG}" 1>&2;;
     :) usage "${INVALID_OPT_ERR} ${OPTARG} ${REQ_ARG_ERR}" 1>&2;;
  esac
done
shift $((OPTIND-1))


#===============================================================================
# variables
#===============================================================================

# get the input file name
input_nopath="${input##*/}"
input_name="${input_nopath%.*}"

# output file name
output_default="${input_name}-detection-$(date +"%Y-%m-%d-%H-%M-%S").txt"

# threshold default
threshold_default='0.3'

# start default
start_default='0.0'


#===============================================================================
# ffprobe video duration
#===============================================================================

ffduration () {
# video duration to append to cutfile
duration_default=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "${input}")

# if video duration is empty exit
[ ! -z "${duration_default}" ] || usage "${input} ${NOT_MEDIA_FILE_ERR}"
}


#===============================================================================
# convert time input to seconds
#===============================================================================

checktime () {
start=$(echo "${start}" | awk -F: 'NF==3 { print ($1 * 3600) + ($2 * 60) + $3 } NF==2 { print ($1 * 60) + $2 } NF==1 { print 0 + $1 }')
end=$(echo "${end}" | awk -F: 'NF==3 { print ($1 * 3600) + ($2 * 60) + $3 } NF==2 { print ($1 * 60) + $2 } NF==1 { print 0 + $1 }')
}


#===============================================================================
# ffmpeg scene detection
#===============================================================================

# scene detection
ffdetection () {
detection="$(ffmpeg -hide_banner -i "${input}" -filter_complex "select='gt(scene,"${threshold:=${threshold_default}}")',metadata=print:file=-" -f null -)"
}

# scene detection range
ffdetection_range () {
detection="$(ffmpeg -hide_banner -i "${input}" \
-filter_complex "[0:0]select='between(t\,"${start}"\,"${end}")'[time];\
[time]select='gt(scene,"${threshold:=${threshold_default}}")',metadata=print:file=-[out]" -map "[out]" -f null -)"
}


#===============================================================================
# create cutfile - prepend start and append end or duration
#===============================================================================

cutfile_seconds () {
echo "${detection}" \
| awk -F':' 'BEGIN { printf("%s\n", '"${start:=${start_default}}"') }/pts_time/ { printf("%s\n", $4) } END { printf("%s\n", '"${end:=${duration_default}}"') }' > "${output:=${output_default}}"
}

cutfile_minutes () {
echo "${detection}" \
| awk -F':' 'BEGIN { printf("%s\n", '"${start:=${start_default}}"') }/pts_time/ { printf("%s\n", $4) } END { printf("%s\n", '"${end:=${duration_default}}"') }' | awk -F. 'NF==1 { printf("%02d:%02d:%02d\n"), ($1 / 3600), ($1 % 3600 / 60), ($1 % 60) }\
NF==2 { printf("%02d:%02d:%02d.%s\n"), ($1 / 3600), ($1 % 3600 / 60), ($1 % 60), ($2) }' \
> "${output:=${output_default}}"
}


#===============================================================================
# run function
#===============================================================================

if [ ! -z "${start}" ] && [ ! -z "${end}" ]; then
# scene detect range in video
  checktime
  ffdetection_range
  if [ ! -z "${format}" ]; then
  cutfile_seconds
  else
  cutfile_minutes
  fi
elif [ ! -z "${start}" ]; then
  usage "${start} ${WRONG_ARGS_ERR}"
elif [ ! -z "${end}" ]; then
  usage "${end} ${WRONG_ARGS_ERR}"
else 
# scene detect entire video
  ffduration
  ffdetection
  if [ ! -z "${format}" ]; then
  cutfile_seconds
  else
  cutfile_minutes
  fi
fi
