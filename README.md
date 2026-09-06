# OWNd

An event listener and command forwarder for the OpenWebNet protocol, used by
BTicino / Legrand MyHOME systems. It is mainly intended to be used from a Home
Assistant integration.

At this point most events are understood. WHO = 5 (Burglar Alarm) event
support is limited and needs further development. Many commands are
implemented, mostly within the requirements of Home Assistant.

> **Fork notice (LGPL-3.0 §4).** This repository is a fork of
> [`anotherjulien/OWNd`](https://github.com/anotherjulien/OWNd) by
> [@anotherjulien](https://github.com/anotherjulien), who wrote the entire
> protocol implementation. It is kept by
> [@foulonc](https://github.com/foulonc) as a controlled mirror for
> [`foulonc/MyHOME`](https://github.com/foulonc/MyHOME). The licence is
> unchanged. Any modification made here will be recorded below and in the
> commit history.

## Why this mirror exists

`foulonc/MyHOME` depends on this library, and the upstream author has stated
in the MyHOME README that they can no longer maintain either project. This
mirror exists so that a private Home Assistant installation is not stranded if
the upstream package or repository becomes unavailable.

## Modifications

**None to the library code.** Every module is byte-for-byte identical to
upstream. Only this README has been changed, to add the fork notice above.

## Releases

| Tag | Commit | Contents |
|---|---|---|
| [`0.7.48`](https://github.com/foulonc/OWNd/releases/tag/0.7.48) | `04603fd` | identical to the `OWNd 0.7.48` release on PyPI |

The `0.7.48` tag is worth explaining, because upstream's history is
misleading here. Two commits carry version `0.7.48` in `setup.py`, and the
**earlier** one, `04603fd`, is what PyPI actually published. This was verified
by hashing all five modules against a running install:

| Module | `04603fd` | `0909ce0` (later 0.7.48) |
|---|---|---|
| `__init__.py` | matches PyPI | matches PyPI |
| `__main__.py` | matches PyPI | differs |
| `connection.py` | matches PyPI | differs |
| `discovery.py` | matches PyPI | matches PyPI |
| `message.py` | matches PyPI | matches PyPI |

The changes in `0909ce0` (logging improvements and a Ctrl+C crash fix) were
never released as 0.7.48; they went out in 0.7.49 instead. The tag here
therefore points at `04603fd`, so it reproduces exactly what a normal
`pip install OWNd==0.7.48` gives you.

## Consuming this mirror

`foulonc/MyHOME` intentionally still requires `OWNd==0.7.48` from PyPI rather
than from here. Home Assistant's `is_installed()` returns `False` for a PEP 508
direct URL reference even when the package is already installed, so a manifest
pointing at this repository would make Home Assistant re-download the tarball
on every startup and fail to set the integration up whenever GitHub is
unreachable.

If PyPI ever stops serving the package, this is the drop-in replacement, and
it has been confirmed to install cleanly:

```
pip install "OWNd @ https://github.com/foulonc/OWNd/archive/refs/tags/0.7.48.tar.gz"
```

## Testing OWNd

Clone this repository and then:

```
cd <OWNd checkout folder>
pip3 install .
python3 -m OWNd --help    # to visualize possible options
```

To attempt connection to the first available OpenWebNet gateway in the local
area network you can run:

```
python3 -m OWNd
```

This will use [SSDP](https://en.wikipedia.org/wiki/Simple_Service_Discovery_Protocol)
to discover all supported gateways and pick the first one.

Alternatively, if you want to skip the SSDP discovery step, you can provide
the IP address, port and MAC address of the gateway from the command line:

```
python3 -m OWNd --address <IP address> --port <PORT> --password <PASS> --mac <MAC address>
```

Note that all these details can be retrieved using the BTicino Home+Project
Android application.

## Known issue carried from upstream

The gateway time request `*#13**0##` crashes the parser in 0.7.48. Avoid
sending it.

## License

LGPL-3.0, unchanged from the original project. See [LICENSE](LICENSE).
