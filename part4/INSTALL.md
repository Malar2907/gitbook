# Installation
<p> To install Geth, open a command line or terminal tool and paste the command below:</p>

```
bash <(curl https://install-geth.ethereum.org)
```
<p>This script will detect your OS and will attempt to install the ethereum CLI.</p><br>

<h3>First Run</h3>
<p>To get started, type the code below on your terminal</p>

```
geth console
```
Once geth is fully started, you should see a `>` prompt, letting you know the console is ready. To exit, type `exit` at the prompt and hit `[enter]`.

<h3>Using stderr</h3>
<p>Output from the console can be logged or redirected:</p>
```
geth console 2>>geth.log
```
Using standard tools, the log can be monitored in a separate window:
```
tail -f geth.log
```
Alternatively, you can also run one terminal with the interactive console and a second one
with the logging output directly.<br>

1. Open two terminals
2. In the <strong>second</strong> terminal type `tty` . The output will be something like `/dev/pts/13`
3. In your main terminal, `type: geth console 2>> /dev/pts/13`<br>

This will allow you to monitor your node without cluttering the interactive console.

<h3>Instructions</h3>
<ul>
<li>Installation Instructions for Mac OS X</li>
<li>Installation Instructions for Windows</li>
<li>Installation Instructions for Linux/Unix</li>
<ol style="color:DodgerBlue;"><li>Ubuntu</li><li>Arch</li><li>FreeBSD</li></ol>
<li>Setup for Raspberry</li><ol style="color:DodgerBlue;"><li>ARM</li></ol><li>Usage instructions for Docker</li></ul>

<p>You can also use a one-line script install Geth. Open a command line or terminal tool (if you
are unsure how to do this, consider waiting for a more user friendly release) and paste the
command below:
```
bash <(curl -L https://install-geth.ethereum.org)
```
This script will detect your OS and will attempt to install the ethereum CLI. For more options
including package managers, check the OS-specific subsections.
















