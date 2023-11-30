#!/usr/bin/env bash
# scriptname: above
# author: Jonathan D. Lettvin, 201401220235

# This finds files of size >= $1 (format ${count}[K|M|G|T], default 10G)
# using a reliable version-independent bash hash to relax find's -size syntax.
# Specifying size using 'T' for Terabytes is supported.
# Output size has units (K|M|G|T) in the left hand output column.

# Example:
#   ubuntu12.04$ above 1T
#   128T /proc/core

# http://stackoverflow.com/questions/1494178/how-to-define-hash-tables-in-bash
# Inspiration for hasch: thanks Adam Katz, Oct 18 2012 00:39
function hasch() { local hasch=`echo "$1" | cksum`; echo "${hasch//[!0-9]}"; }
function usage() { echo "Usage: $0 [{count}{k|K|m|M|g|G|t|T}"; exit 1; }
function arg1() {
    # Translate single arg (if present) into format usable by find.
    count=10; units=G;  # Default find -size argument to 10G.
    size=${count}${units}
    if [ -n "$1" ]; then
        for P in TT tT GG gG MM mM Kk kk; do xlat[`hasch ${P:0:1}`]="${P:1:1}"; done
        units=${xlat[`hasch ${1:(-1)}`]}; count=${1:0:(-1)}
        test -n "$units" || usage
        test -x $(echo "$count" | sed s/[0-9]//g) || usage
        if [ "$units" == "T" ]; then units="G"; let count=$count*1024; fi
        size=${count}${units}
    fi
}
function main() {
    sudo \
        find / -type f -size +$size -exec ls -lh {} \; 2>/dev/null | \
        awk '{ N=$5; fn=$9; for(i=10;i<=NF;i++){fn=fn" "$i};print N " " fn }'
}

arg1 $1
main $size
