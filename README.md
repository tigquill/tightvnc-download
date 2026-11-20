# TightVNC 2.8.90 Download for Windows 10/11 — VNC Server and Viewer

TightVNC is a remote desktop package built around the RFB protocol, with its own Tight encoding that compresses screen updates harder than plain VNC. The Windows MSI installs two programs: a service-mode server that starts before login, and a viewer for connecting to it. Screen updates travel over TCP 5900 by default, and the server can serve the console session, a mirrored session or a separate virtual desktop.

RFB is a thin protocol: the server sends framebuffer updates as rectangles, the client sends pointer and keyboard events back. Everything else — encoding, authentication, clipboard, file transfer — is negotiated at connect time. Tight encoding is the reason this fork exists: it applies zlib to the screen data and optionally JPEG to photographic regions, so a photo-heavy desktop costs far less bandwidth than with raw or hextile encoding. The trade-off is CPU time on both ends, which is why the encoding level and JPEG quality are adjustable per connection.

<p align="center">
  <a href="https://www.tightvnc.com/download.php">
    <img src="https://img.shields.io/badge/DOWNLOAD-TightVNC_2.8.90-1B5E9B?style=for-the-badge&logo=windows&logoColor=white" alt="Download TightVNC">
  </a>
</p>

| | |
|---|---|
| **Version** | 2.8.90 |
| **Package** | installer from the download page |
| **Architecture** | x64 / x86 |
| **Platform** | Windows 10, Windows 11, Windows Server 2012+ |
| **License** | GPLv2 |

## Download

| Package | Link |
|---|---|
| **TightVNC 2.8.90** | [Download](https://www.tightvnc.com/download.php) |

The link opens the download page, where the 2.8.90 Windows installers sit together with the current major version. The MSI on that page is the published package, byte for byte.

## Works with

- Windows 11 (x64)
- Windows 10 22H2 and newer
- Windows Server 2016 / 2019 / 2022
- Viewer also on Linux, macOS and Raspberry Pi
- Windows XP and later, according to the build notes for 2.8.90

## SHA-256 checksum

`SHA256SUMS.txt` sits in the releases tab beside the installer; read the line for the 64-bit MSI and hash your copy:

```powershell
Get-FileHash -Algorithm SHA256 <path-to-installer>
```

If the digests disagree, delete the MSI and download it again — never install from a file that failed the check.

## Install

1. Download the 64-bit MSI and run it, or deploy it through Group Policy.
2. Choose **Custom** to install only the server or only the viewer.
3. Set the access password and, if needed, a separate view-only password in the server properties.
4. Silent install: `msiexec /i tightvnc-2.8.90-gdi-setup-64bit.msi /qn ADDLOCAL=Server,Viewer`.

## What it does

- Server runs as a Windows service, so the desktop is reachable before anyone logs in
- Tight encoding plus JPEG and zlib levels for slow links
- File transfer between viewer and server without a separate SMB share
- Reverse connections: the server dials out to a listening viewer, which passes NAT
- MSI package with ADDLOCAL components for scripted deployment

## Compatibility

The server and viewer speak standard RFB, so a TightVNC viewer can open a session on another VNC server and a third-party viewer can reach a TightVNC server. Features beyond the base protocol are the exception: file transfer and the Tight-specific encoding settings only work when both ends are TightVNC, and a RealVNC or TigerVNC peer falls back to a simpler encoding. Display numbers map to ports in the usual way — display 0 is 5900, display 1 is 5901 — and the server's HTTP port for the Java viewer sits 100 below, at 5800 for the first display.

The MSI carries both components and a set of properties that can preset the access password, the RFB port and the firewall exception during a deployment, which is why the package suits Group Policy and management tools better than a plain setup executable. Installing only the server is the common choice on managed desktops; the viewer alone is enough on an administrator's workstation.

Two limitations come from the protocol rather than the build. VNC authentication accepts a password of up to eight characters, and anything typed beyond that is silently ignored at both ends. Traffic is not encrypted in this version, so a session over the open internet needs a tunnel — SSH port forwarding or a VPN — unless the network itself is trusted. The service-mode server does capture the logon screen and UAC prompts, because it runs in session 0 with system rights.

## Troubleshooting

**`Connection refused`** — the server service is stopped or the firewall blocks 5900. Start `tvnserver` and allow the port on the profile the network is using.

**`Authentication failed`** — the password is wrong, or it was set to more than eight characters and only the first eight are being compared. Reset it in the server properties.

**Black screen instead of the desktop** — the server runs in user mode and cannot see the secure desktop. Switch it to service mode, which captures the logon screen as well.

**Viewer connects and immediately closes** — the server has reached its connection limit, or the client requested an encoding the server refuses. Try a different encoding in the viewer options.

**Ctrl-Alt-Del does nothing** — the key combination is intercepted by Windows on the local machine. Use the viewer's **Send Ctrl-Alt-Del** command.

**Screen updates lag on a slow link** — lower the JPEG quality and raise the compression level, or drop the colour depth to 8-bit for text work.

## Alternatives

- **UltraVNC** — Windows-focused fork with plugins, MS Logon and a repeater.
- **RealVNC** — commercial, with cloud routing and encrypted transport out of the box.
- **TigerVNC** — the usual choice on Linux desktops, with a Windows viewer.
- **RustDesk** — a modern alternative with its own relay and no port forwarding.

## FAQ

**Is TightVNC free?** — Yes, GPLv2 for the 2.8.90 build, with no time limit and no seat count.

**Does it support Windows 11?** — Yes, and the same binary runs on older versions back to XP according to the release notes.

**Can it be installed silently?** — Yes, through the MSI with `ADDLOCAL` and `/qn`, which also allows the password and port to be preset.

**Is the connection encrypted?** — Not in this version. The password challenge is not a secure channel; wrap the session in SSH or a VPN when it crosses an untrusted network.

**Can I connect to a machine behind NAT?** — Yes, with a reverse connection: the viewer listens and the server dials out to it, so no inbound port is needed on the remote side.

**Does the viewer run on Linux?** — Yes, and on macOS and Raspberry Pi; the server side is Windows only.

**Can several viewers watch one desktop?** — Yes, the server accepts multiple simultaneous connections, and view-only clients can be restricted from sending input.

## Disclaimer

The version and installer details above were recorded from the published release for offline reference. TightVNC is distributed under the GNU General Public License version 2, and the name belongs to its authors. This page is an independent index with no affiliation to the project and implies no endorsement.
