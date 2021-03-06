## ownCloud-share-tools

ownCloud share tools is an set of tools that provide access to the ownCloud OCS Share API. In this repository you will find the command line client, GUI and Python library for interfacing with ownCloud shares.

***

## Requirements
Python 3
PyQt5
requests

To install these on a debian based system, sudo apt-get install python3 python3-pyqt5 python-requests

***

## Install

Run python3 setup.py install

***

## The GUI

![](http://i.imgur.com/VQ5vG24.png)
ownCloud-share-tools running on Linux as a Thunar custom action.

The GUI is designed to be used as a file browser action.
To add the GUI to Thunar, open Thunar and click edit, configure custom actions, click add then in the command box enter

`ocsharetools --user YourUserName --pass YourPassword --url http://example.com/owncloud gui --path %F`

If you are connecting to an OwnCloud server that uses self-signed certificates enter

`ocsharetools --user YourUserName --pass YourPassword --url http://example.com/owncloud gui  --disable-ssl-verification --path %F`

All the other text boxes can be set to whatever you want, although setting the name to ownCloud and using the ownCloud icon is recommended.

***

## Using the CLI
```
$ ocsharetools --help
usage: ocsharetools.py [-h] --username USERNAME --password PASSWORD --url URL
                       [--disable-ssl-verification]
                       {getshares,getshare,create,update,delete,gui} ...

Perform OCS Share API calls

positional arguments:
  {getshares,getshare,create,update,delete,gui}
                        Available commands
    getshares           get Shares from a specific file or folder
    getshare            get a single share by id
    create              create a share
    update              update a share
    delete              delete a share by id
    gui                 run gui

optional arguments:
  -h, --help            show this help message and exit
  --username USERNAME   Your OwnCloud username
  --password PASSWORD   Your OwnCloud password
  --url URL             Your OwnCloud url, eg https://example.com/owncloud/
  --disable-ssl-verification
                        Disables SSL verification, eg when the OwnCloud server
                        is using self-signed certificates

  ```

***

######Examples:
Get a list of shares

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud getshares```

Create a public share link with a password

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud create --path /NewDocument.odt --share-type=3 --share-password secret```

Create a share with a user

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud create --path /NewDocument.odt --share-type=0 --share-with=Steve```

Create a share with a group

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud create --path /NewDocument.odt --share-type=1 --share-with=Developers```

Delete a share (ID number is obtained from getshares command)

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud delete 32```

Update a share a share (ID number is obtained from getshares command), setting an expiry date

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud update 32 --expire-date "31-01-2015"```

Get a list of shares on a server that uses self-signed certificats for SSL encryption

```ocsharetools --user Bob --pass secret --url http://example.com/ownCloud --disable-ssl-verification getshares```


## Using the library

```python
# First off, import the library
from ocsharetools import *

# Initialise the library with your url, username and password
ocs = OCShareAPI('http://example.com/ownCloud', 'Bob', 'secret')

# Create a share
share = ocs.create_share(path='/NewDocument.odt', share_type=3)

# Print share URL
print(share.url)

# Set the expiry date to tomorrow.
import datetime
date = datetime.date.today() + datetime.timedelta(days=1)

share.update(expire_date=date)

# Delete the share
share.delete()
```

## Troubleshooting

If the connection to the OwnCloud server refuses to work with the error message similar to

```requests.exceptions.SSLError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed (_ssl.c:598)```

then the server is most likely using a self-signed certificate, which cannot be verified. In this case use the
**--disable-ssl-verification** command line option, eg for listing the shares on the server use

```ocsharetools.py --username USERNAME --password PASSWORD --url URL --disable-ssl-verification getshares```
