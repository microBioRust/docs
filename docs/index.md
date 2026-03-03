# Welcome to micro**BioRust**
 
A blazing-fast, sustainable bioinformatics toolkit written in [Rust](https://www.rust-lang.org/) — for microbial genomics research, and optimised for functions used in data exploration.  

---

## Features

- 🦀 Built in Rust programming language for speed and safety
- 🔄 Python bindings _via_ pyo3 for InterOp - Rust meets Python
- 📦 Open source and community-driven

---

## Get Started!!
See Installation for details on how to install Rust for Linux, MacOSX and Windows

If you want to call microbiorust from Python, that is microbiorust-py:
##Interested in microbiorust-py? Check out the microbiorust-py section for quick-start & more! 

for ```microbiorust-py``` install with pip, no need to install Rust - see the microbiorust-py section

Start a new project
```cargo new microBioRust_test```

Add to your Cargo.toml
```cargo add -p microBioRust```

to add the whole workspace including file parsing, sequence metrics, coming soon data viz (heatmap demonstration) and python bindings (microbiorust-py)

```cargo add -p seqmetrics```  
```cargo add -p heatmap```  
```cargo add -p microbiorust-py```

or clone the repo  
```git clone https://github.com/microBioRust/microBioRust.git```

Build the project  
```cargo build```

Run the tests  
```cargo test```

