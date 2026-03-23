# Running LAVA

## 1. Setup

### 1a. Download reference data

Download the reference data from **[Zenodo](https://zenodo.org/records/19191337)**, then run:

```bash
mkdir ref/
mv lava_ref.zip ref/
cd ref/
unzip lava_ref.zip
cd ..
```

---

## 1b. Check if Python and R are installed:

```bash
python --version
R --version
```

Also, check whether pandas is installed: 

```bash
pip list
```

**if not do the following**

```bash
pip install pandas
```

**Also i have commented (#) all of the packages required for installation in case an error pops up asa results of that at the very top of the Rscript**

---

2. Clone the repo
   
```bash
git clone https://github.com/guillermocomesanacimadevila/LAVA.git
cd LAVA/
```

3. Prep data!

```bash
mkdir data/ 
```

**Dump your summary stats here**

---

You data needs to be in this format before though running any of the scripts (example: AD.tsv)

```bash
SNP	A1	A2	FRQ	N	BETA	SE	P	CHR	POS
rs10875231	T	G	0.2406	88394.0	-0.0026	0.0168	0.8758	1	100000012
rs6678176	T	C	0.2926	88394.0	0.0008	0.0156	0.9574	1	100000827
rs78286437	T	C	0.9477	88394.0	-0.0136	0.033	0.6792	1	100000843
rs144406489	A	G	0.9769	88394.0	-0.0061	0.0612	0.9204	1	100001138
```

4. Make info file

```bash
mkdir info && cd info/
```

```bash
cat <<EOF > input.info_ad_scz.txt
phenotype	cases	controls	prevalence	filename
AD	21982	41944	0.07	AD.tsv
SCZ	67390	94015	0.01	SCZ.tsv
EOF
```

---

5. Assemble intercept matrix (from LDSC output!) - stay within info/! 

**It has to be a 2x2 matrix!**

```bash
cat <<EOF > intercept_matrix_ad_scz_ldsc.csv
,AD,SCZ
AD,1,0.0130185429239193
SCZ,0.0130185429239193,1
EOF
```

---

6. Harmonise your summary stats to LAVAs format

```bash
python3 bin/prep_data.py \
  -i data/AD.tsv \
  -o data/AD_lava_ready.tsv
```

```bash
python3 bin/prep_data.py \
  -i data/SCZ.tsv \
  -o data/SCZ_lava_ready.tsv
```

---

7. Run LAVA!

```bash
mkdir results/
```

**Now run from the main directory (e.g. /Users/cXXXXXXXX/lava/)**

```bash
Rscript bin/lava_pair.R \
  ref/lava_ref/lava-ukb-v1.1 \
  ref/lava_blocks.coords.loci \
  info/input.info_ad_scz.txt \
  info/intercept_matrix_ad_scz_ldsc.csv \
  results/AD_SCZ \
  AD 21982 41944 0.07 data/AD_lava_ready.tsv \
  SCZ 67390 94015 0.01 data/SCZ_lava_ready.tsv
```
