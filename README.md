# paper_work
The code and materials of GeneMiner2

# Simulated data
### Generate sequence files
```
mkdir -p fasta
tree_file="standard.tree" && seed=1000 && for i in $(seq 1 1000); do fasta_file="seq/$i.fasta"; seq-gen -mGTR -l1000 -n1 -f0.25,0.25,0.25,0.25 -z $seed -of < "$tree_file" > "$fasta_file"; seed=$((seed + 2)); done
```

### Reference sequence mutation, mutation rate 5-20%
mutate.py


### Sequence renaming
```
for file in *.fasta; do awk '/^>/{print ">"substr(FILENAME,1,length(FILENAME)-6)"-"substr($0,2)} !/^>/{print}' "$file" > "rename/$file"; done
```

### Merge five species
merge.sh


### Extend 200bp
200bp.py

### ngsngs
ngs.sh

### Rename sequences in mutated files >1-A
```
mkdir -p assemble_ref
for file in mutated_*.fasta; do mv "$file" "${file/mutated_/}"; done

for file in *.fasta; do awk '/^>/{print ">"substr(FILENAME,1,length(FILENAME)-6)"-"substr($0,2)} !/^>/{print}' "$file" > "ass_ref/$file"; done
```

### Merge ref files for conventional method
```
cd assemble_ref
cat *.fasta >ref.fasta
```

---

### conventional(megahit+blast), for 1-10x and A-J respectively

runmega.sh

blast.sh

run_extract.sh (calls extract.py)

combine.py

trim.sh

### Alignment and trimming
```
mkdir align
for f in *.fasta; do mafft --auto "$f" > "align/${f%.fasta}.fasta"; done
mkdir -p trim
for f in *.fasta; do trimal -in "$f" -out "trim/${f%.fasta}_trimmed.fasta" -automated1; done
```

## Concatenated tree
### Concatenation
```
seqkit concat -n *.fasta > concatenated.fasta
```

### Alignment + trimming
```
mafft --auto concatenated.fasta > concatenated.aln
trimal -automated1 -in concatenated.aln -out concatenated.trimmed.fasta
```

### Tree building
```
FastTree -nt -gtr -gamma -boot 500 concatenated.trimmed.fasta > supermatrix_tree.nwk

for f in *_trimmed.fasta; do
    FastTree -nt -gtr -gamma "$f" > "${f%.fasta}.tree"
done

ls *.tree > tree_list.txt
java -jar astral.5.7.1.jar -i tree_list.txt -o species_tree.nwk
```

---
## Tree accuracy statistics

remove_sup.py

rf.py

heatmap.py

FN.py

- Result files

result.zip/heatmap.xlsx

---
## Sequence accuracy statistics

score2.py

count4.py

duiji_level.py

error_rate_cal.py

error_rate_figs.py

- Result files

result.zip/combine_qs20_ref0.1.xlsx

result.zip/combine_qs20_ref0.05.xlsx

result.zip/error_rate/ref0.1

result.zip/error_rate/ref0.1

# benchmarking pipeline code (Apiaceae)

code.zip/geneminer

code.zip/r2t

code.zip/hybpiper
