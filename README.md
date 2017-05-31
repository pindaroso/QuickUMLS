**We recommend to download the latest tested version from the [releases section](https://github.com/Georgetown-IR-Lab/QuickUMLS/releases)**.

**NEW: v.1.2 now includes client/server support!** Start a QuickUMLS server once, avoid loading QuickUMLS each time your experiments run!

# QuickUMLS

QuickUMLS (Soldaini and Goharian, 2016) is a tool for fast, unsupervised  biomedical concept extraction from medical text.
It takes advantage of [Simstring](http://www.chokkan.org/software/simstring/) (Okazaki and Tsujii, 2010) for approximate string matching.
For more details on how QuickUMLS works, we remand to our paper.

This project should be compatible with both Python 2 and 3 and run on any UNIX system (sorry, no Windows support). **If you find any bugs, please file an issue on GitHub or email the author at luca@ir.cs.georgetown.edu**.

## Installation

#### Before Starting

1. Make sure that your Python installation include C headers (e.g., on Ubuntu, make sure `python3-dev` or `python-dev` are installed).
2. This software requires all packages listed in the `requirements.txt` file. You can install all of them by running `pip install -r requirements.txt`.
3. Note that, in order to use `spacy`, you are required to download its corpus. You can do that by running `python -m spacy.en.download`.
4. This system requires you to have a valid UMLS installation on disk. The installation can be remove once the system has been initialized.

#### How To get the System Initialized

1. Download and compile Simstring by running `bash setup_simstring.sh <python_version>`, where `<python_version>` is either "`2`" or "`3`".
2. Initialize the system by running `python install.py <umls_installation_path> <destination_path>`, where `<umls_installation_path>` is where the installation files are (in particular, we need `MRCONSO.RRF` and `MRSTY.RRF`) and `<destination_path>` is the directory where the QuickUmls data files should be installed. This process will take between 5 and 30 minutes depending how fast the CPU and the drive where UMLS and QuickUMLS files are stored are (on a system with a Intel i7 6700K CPU and a 7200RPM hard drive, initialization takes 8.5 minutes).

`install.py` supports the following optional arguments:
- `-L` / `--lowercase`: if used, all concept terms are folded to lowercase before being processed. This option typically increases recall, but it might reduce precision;
- `-U` / `--normalize-unicode`: if used, expressions with non-ASCII characters are converted to the closest combination of ASCII characters.
- `-E` / `--language`: Specify the language to consider for UMLS concepts; by default, English is used. For a complete list of languages, please see [this table provided by NLM](https://www.nlm.nih.gov/research/umls/knowledge_sources/metathesaurus/release/abbreviations.html#LAT).

## APIs

A QuickUMLS object can be instantiated as follows:

```python
matcher = QuickUMLS(quickumls_fp, overlapping_criteria, threshold,
                    similarity_name, window, accepted_semtypes)
```

Where:

- `quickumls_fp` is the directory where the QuickUMLS data files are installed.
- `overlapping_criteria` (optional, default: "score") is the criteria used to deal with overlapping concepts; choose "score" if the matching score of the concepts should be consider first, "length" if the longest should be considered first instead.
- `threshold` (optional, default: 0.7) is the minimum similarity value between strings.
- `similarity_name` (optional, default: "jaccard") is the name of similarity to use. Choose between "dice", "jaccard", "cosine", or "overlap".
- `window` (optional, default: 5) is the maximum number of tokens to consider for matching.s
- `accepted_semtypes` (optional, default: see `constants.py`) is the set of UMLS semantic types concepts should belong to. Semantic types are identified by the letter "T" followed by three numbers (e.g., "T131", which identifies the type *"Hazardous or Poisonous Substance"*). See [here](https://metamap.nlm.nih.gov/Docs/SemanticTypes_2013AA.txt) for the full list.

To use the matcher, simply call

```python
text = "The ulna has dislocated posteriorly from the trochlea of the humerus."
matcher.match(text, best_match=True, ignore_syntax=False)
```

Set `best_match` to `False` if you want to return overlapping candidates, `ignore_syntax` to `True` to disable all heuristics introduced in (Soldaini and Goharian, 2016).


## References

- Okazaki, Naoaki, and Jun'ichi Tsujii. "*Simple and efficient algorithm for approximate dictionary matching.*" COLING 2010.
- Luca Soldaini and Nazli Goharian. "*QuickUMLS: a fast, unsupervised approach for medical concept extraction.*" MedIR Workshop, SIGIR 2016.
