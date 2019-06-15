# Notes on a small Ubuntu NAS with ZFS and Docker
Scot W. Stevenson <scot.stevenson@gmail.com>   
First version: 15. June 2019
This version: 15. June 2019

This document lists the steps to creating a NAS based on Ubuntu Server 18.04 LTS
with the ZFS filesystem for bulk storage and container (Docker) support. **This
is just documentation, there is no code.** It is provided here in case it proves
helpful for other people looking to build their own NAS from scratch. 

Note that this is just the GitHub repository for the raw files. The documents
themselves are best read at Read the Docs at https://ubuntu-nas.readthedocs.io/.
In fact, one major goal of this text was to experiment with reStructured text,
Sphinix, and Read the Docs for other projects.

## Liability

Follow these steps at your own risk. The guide is for my own personal system
(which some changes for security reasons) and probably will not work for yours.
It most likely also contains errors, and is certainly not a state of the art way
to build a server. This system is used in a low-threat environment, and security
is not a priority. Also, I'm not a computer professional. In other words, I take
no responsibility, especially if you lose your data, which is a very real
posibility. Be scared.

## Errors and updates

I'll be happy to accept corrections, additions, and suggestions as pull
requests. 

## Cats

No cats were harmed during the creation of this document, or even woken up. 




