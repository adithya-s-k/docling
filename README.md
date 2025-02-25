<p align="center">
  <a href="https://github.com/ds4sd/docling">
    <img loading="lazy" alt="Docling" src="https://github.com/DS4SD/docling/raw/main/logo.png" width="150" />
  </a>
</p>

# Docling

[![PyPI version](https://img.shields.io/pypi/v/docling)](https://pypi.org/project/docling/)
![Python](https://img.shields.io/badge/python-3.10%20%7C%203.11%20%7C%203.12-blue)
[![Poetry](https://img.shields.io/endpoint?url=https://python-poetry.org/badge/v0.json)](https://python-poetry.org/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Imports: isort](https://img.shields.io/badge/%20imports-isort-%231674b1?style=flat&labelColor=ef8336)](https://pycqa.github.io/isort/)
[![Pydantic v2](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/pydantic/pydantic/main/docs/badge/v2.json)](https://pydantic.dev)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit)
[![License MIT](https://img.shields.io/github/license/DS4SD/docling)](https://opensource.org/licenses/MIT)

Docling bundles PDF document conversion to JSON and Markdown in an easy, self-contained package.

## Features
* ⚡ Converts any PDF document to JSON or Markdown format, stable and lightning fast
* 📑 Understands detailed page layout, reading order and recovers table structures
* 📝 Extracts metadata from the document, such as title, authors, references and language
* 🔍 Optionally applies OCR (use with scanned PDFs)

## Installation

To use Docling, simply install `docling` from your package manager, e.g. pip:
```bash
pip install docling
```

> [!NOTE]
> Works on macOS and Linux environments. Windows platforms are currently not tested.

### Development setup

To develop for Docling, you need Python 3.10 / 3.11 / 3.12 and Poetry. You can then install from your local clone's root dir:
```bash
poetry install --all-extras
```

## Usage

### Convert a single document

To convert invidual PDF documents, use `convert_single()`, for example:
```python
from docling.document_converter import DocumentConverter

source = "https://arxiv.org/pdf/2206.01062"  # PDF path or URL
converter = DocumentConverter()
doc = converter.convert_single(source)
print(doc.export_to_markdown())  # output: "## DocLayNet: A Large Human-Annotated Dataset for Document-Layout Analysis [...]"
```

### Convert a batch of documents

For an example of converting multiple documents, see [convert.py](https://github.com/DS4SD/docling/blob/main/examples/convert.py).

From a local repo clone, you can run it with:

```
python examples/convert.py
```
The output of the above command will be written to `./scratch`.

### Adjust pipeline features

#### Control pipeline options

You can control if table structure recognition or OCR should be performed by arguments passed to `DocumentConverter`:
```python
doc_converter = DocumentConverter(
    artifacts_path=artifacts_path,
    pipeline_options=PipelineOptions(
        do_table_structure=False,  # controls if table structure is recovered
        do_ocr=True,  # controls if OCR is applied (ignores programmatic content)
    ),
)
```

#### Control table extraction options

You can control if table structure recognition should map the recognized structure back to PDF cells (default) or use text cells from the structure prediction itself.
This can improve output quality if you find that multiple columns in extracted tables are erroneously merged into one.


```python
pipeline_options = PipelineOptions(do_table_structure=True)
pipeline_options.table_structure_options.do_cell_matching = False  # uses text cells predicted from table structure model

doc_converter = DocumentConverter(
    artifacts_path=artifacts_path,
    pipeline_options=pipeline_options,
)
```

### Impose limits on the document size

You can limit the file size and number of pages which should be allowed to process per document:
```python
conv_input = DocumentConversionInput.from_paths(
    paths=[Path("./test/data/2206.01062.pdf")],
    limits=DocumentLimits(max_num_pages=100, max_file_size=20971520)
)
```

### Convert from binary PDF streams

You can convert PDFs from a binary stream instead of from the filesystem as follows:
```python
buf = BytesIO(your_binary_stream)
docs = [DocumentStream(filename="my_doc.pdf", stream=buf)]
conv_input = DocumentConversionInput.from_streams(docs)
converted_docs = doc_converter.convert(conv_input)
```
### Limit resource usage

You can limit the CPU threads used by Docling by setting the environment variable `OMP_NUM_THREADS` accordingly. The default setting is using 4 CPU threads.


## Contributing

Please read [Contributing to Docling](https://github.com/DS4SD/docling/blob/main/CONTRIBUTING.md) for details.


## References

If you use Docling in your projects, please consider citing the following:

```bib
@software{Docling,
author = {Deep Search Team},
month = {7},
title = {{Docling}},
url = {https://github.com/DS4SD/docling},
version = {main},
year = {2024}
}
```

## License

The Docling codebase is under MIT license.
For individual model usage, please refer to the model licenses found in the original packages.
