# Windows
<h3>Installing from Chocolatey</h3>
For master branch:
```
choco install geth-stable
```
For more information see https://chocolatey.org/packages/geth-stable
<p>For develop branch:</p>
```
choco install geth-latest
```
For more information see https://chocolatey.org/packages/geth-latest

<h3>Building from source</h3>
1. Install Git from http://git-scm.com/downloads
2. Install Golang from https://storage.googleapis.com/golang/go1.4.2.windows-amd64.msi
3. Install winbuilds from http://win-builds.org/1.5.0/win-builds-1.5.0.exe to c:\winbuilds
4. Run win builds here. It's safe to remove big dependencies like QT and GTK which aren't
needed. An exact list of dependencies should be determined
5. Setup environment paths
i. Add `GOROOT` pointed to `c:\go` and `GOPATH` to `c:\godev` (you are free to pick
these paths).
ii. Set `PATH` to `%PATH%`;`%GOROOT%\bin`;`%GOPATH%\bin`;`c:\winbuilds\bin`
6. Open a terminal and install godep first: `go get -u github.com/tools/godep`
7. Open a terminal and download go-ethereum `go get -d -u github.com/ethereum/goethereum`
8. Try building ethereum with go dep, navigate to `c:\godev\src\github.com\ethereum\goethereum\cmd\geth`
and run `git checkout develop && godep go install`
If you want to build from an other branch bypass `godep go install` for `go install`and
checkout the dependencies manually.

<h5>Powershell script for building with Cygwin</h5>
```
#REQUIRES -Version 3.0
# Set local directory paths
$basedir = $env:USERPROFILE
$downloaddir = "$basedir\Downloads"

# Set Go variables
$golangroot = "$basedir\golang"
$gosrcroot = "$basedir\go"
$golangdl = "https://storage.googleapis.com/golang/"

# Set cygwin variables
$cygwinroot = "$basedir\cygwin"
$cygwinpackages = "gcc-g++,binutils,make,git,gmp,libgmp10,libgmp-devel"
$cygwinmirror = "http://cygwin.mirrorcatalogs.com"
$cygwindl = "https://cygwin.com/"

# Finalize paths based on processor architecture
if ($ENV:PROCESSOR_ARCHITECTURE -eq "AMD64") {
$golangdl = $golangdl + "go1.5.1.windows-amd64.zip"
$cygwindl = $cygwindl + "setup-x86_64.exe"
} else {
$golangdl = $golangdl + "go1.5.1.windows-386.zip"
$cygwindl = $cygwindl + "setup-x86.exe"
}

# Download dependencies
Invoke-WebRequest $cygwindl -UseBasicParsing -OutFile "$downloaddir\cygwin-setup.exe"
Invoke-WebRequest $golangdl -UseBasicParsing -OutFile "$downloaddir\golang.zip"
# Install Cygwin & dependencies
Invoke-Expression "$downloaddir\cygwin-setup.exe --root $cygwinroot --site $cygwinmirror --no-admin --quiet-mode --packages=$cygwinpackages"

# Install Golang
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::ExtractToDirectory("$downloaddir\golang.zip", $golangroot)

# Set environment variables
# Only works locally
$env:GOROOT = "$golangroot\go"
$env:GOPATH = $gosrcroot
$env:PATH = "$env:PATH;$cygwinroot\bin;$golangroot\go\bin;$gosrcroot\bin"
# Only works in new sessions
[Environment]::SetEnvironmentVariable("GOROOT", $env:GOROOT, "User")
[Environment]::SetEnvironmentVariable("GOPATH", $env:GOPATH, "User")
[Environment]::SetEnvironmentVariable("PATH", $env:PATH, "User")

# Download go-ethereum source
go get github.com/tools/godep
git clone https://github.com/ethereum/go-ethereum $env:GOPATH/src/github.com
cd $env:GOPATH/src/github.com/ethereum/go-ethereum
godep go install .\cmd\geth
```
<h3>Running in Docker</h3>
We keep a Docker image with recent snapshot builds from the `develop` branch on
DockerHub. Run this first:
```
docker pull ethereum/client-go
```
Start a node with:
```
docker run -it -p 30303:30303 ethereum/client-go
```
To start a node that runs the JSON-RPC interface on port <strong>8545</strong>, run:
```
docker run -it -p 8545:8545 -p 30303:30303 ethereum/client-go --rpc
```
<strong>WARNING: This opens your container to external calls</strong>
<p>To use the interactive JavaScript console, run:</p>
```
docker run -it -p 30303:30303 ethereum/client-go console
```

