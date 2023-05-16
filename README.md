# nrfcredstore

nrfcredstore is a command line tool that simplifies managing credentials stored in Nordic Semiconductor modems, like the [nRF9160](https://www.nordicsemi.com/products/nrf9160). The typical use case of nrfcredstore is to automate the provisioning of cloud certificates that are stored securely in the modem.

## Install

Run the following command to use this package as a dependency:

    pip3 install nrfcredstore

## Requirements

For the device to respond to AT commands, the firmware on the device must have at_client enabled. This can be done by setting `CONFIG_AT_HOST_LIBRARY=y`. The [nRF9160: AT Client sample](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/samples/nrf9160/at_client/README.html) can also be used, and the [nRF9160 DK application and modem firmware](https://www.nordicsemi.com/Products/Development-hardware/nRF9160-DK/Download#infotabs) download contains a pre-built firmware.

## Command Line Interface

```
usage: nrfcredstore [-h] [--baudrate BAUDRATE] [--timeout TIMEOUT] dev {list,write,delete,generate} ...

Manage certificates stored in a cellular modem.

positional arguments:
  dev                   Serial device used to communicate with the modem.

options:
  -h, --help            show this help message and exit
  --baudrate BAUDRATE   Serial baudrate
  --timeout TIMEOUT     Serial communication timeout in seconds

subcommands:
  {list,write,delete,generate}
                        Certificate related commands
    list                List all keys stored in the modem
    write               Write key/cert to a secure tag
    delete              Delete value from a secure tag
    generate            Generate private key
```

### list subcommand

List keys stored in the modem.

```
usage: nrfcredstore [--baudrate BAUDRATE] [--timeout TIMEOUT] dev list [--tag SECURE_TAG [--type KEY_TYPE]]
```

#### example

```
$ nrfcredstore /dev/tty.usbmodem0009600000001 list --tag 123
Secure tag   Key type           SHA
123          ROOT_CA_CERT       XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
123          CLIENT_CERT        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
123          CLIENT_KEY         XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

### write subcommand

Write key/cert to a secure tag. KEY_TYPE must be either ROOT_CA_CERT, CLIENT_CERT, CLIENT_KEY, or PSK.

```
usage: nrfcredstore [--baudrate BAUDRATE] [--timeout TIMEOUT] dev write SECURE_TAG KEY_TYPE FILENAME
```

#### example

    $ nrfcredstore /dev/tty.usbmodem0009600000001 write 123 ROOT_CA_CERT root-ca.pem

### delete subcommand

Delete value from a secure tag.

```
usage: nrfcredstore [--baudrate BAUDRATE] [--timeout TIMEOUT] dev delete SECURE_TAG KEY_TYPE
```

#### example

    $ nrfcredstore /dev/tty.usbmodem0009600000001 delete 123 ROOT_CA_CERT

### generate subcommand

Generate a private key in the modem and output a certificate signing request.

```
usage: nrfcredstore [--baudrate BAUDRATE] [--timeout TIMEOUT] dev generate SECURE_TAG FILENAME
```

#### example

    $ nrfcredstore /dev/tty.usbmodem0009600000001 generate 123 device_cert.der

    # Convert DER to CSR
    $ openssl req -pubkey -in device_cert.der -inform DER > device_cert.csr

## Development installation

For development mode, you need [poetry](https://python-poetry.org/):

    curl -sSL https://install.python-poetry.org | python3 -

Install package dependencies, development dependencies, and the nrfcredstore itself into poetry's internal virtual environment:

    poetry install

## Test

Running the tests depends on a [development installation](#development-installation).

    poetry run pytest
