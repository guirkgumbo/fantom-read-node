This document contains instructions for setting up a Fantom Read-only node on the Fantom mainnet.
You can find the offical docs for the current version here https://github.com/Fantom-foundation/lachesis_launch 

These instructions have been developed to configure a Fantom Read-only node using Ubuntu 20.04 LTS on VM with 2TB SSD and 24GB RAM. These instructions are primarily for my own purposes, so that I can recreate my environment if I need to. They are not intended to represent best practices and may not be applicable to your hardware, software, or network configuration. There are many other good sources for instructions on setting up these services, and those may be more generally written and applicable.

Setup includes installation and configuration of the following services, including setting up systemd to automatically run services, where applicable:

  Ubuntu Prerequisities
  General Ubuntu Security and Monitoring
  Go-Opera Read-only node