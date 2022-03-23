## HPC on Cloud by IBM Research

Collection of "HPC on Cloud" tutorials

## Building the Workshop site

The content of the workshops is built using [hugo](https://gohugo.io/).

To build the content
1. [Install Hugo](https://gohugo.io/getting-started/installing/). Assuming you have Homebrew installed, on a Mac or Linux (including WSL), simply
```bash
brew install hugo
```
2. Clone this repository, including submodules
```bash
git clone --recurse-submodules git@github.com:IBM/ibmcloud-hpc-tutorials.git
```
3. Run hugo to generate the site
```bash
cd ibmcloud-hpc-tutorials
hugo
hugo server -D
```
4. Point your browser to http://localhost:1313/ibmcloud-hpc-tutorials/

## License

The documentation is made available under the Creative Commons Attribution-ShareAlike 4.0 International License. See the [LICENSE](LICENSE) file.

The sample code within this documentation is made available under the MIT-0 license. See the [LICENSE-SAMPLECODE](LICENSE-SAMPLECODE) file.
