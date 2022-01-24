Introduction
============

.. image:: https://readthedocs.org/projects/adafruit-circuitpython-ble_magic_light/badge/?version=latest
    :target: https://docs.circuitpython.org/projects/ble_magic_light/en/latest/
    :alt: Documentation Status

.. image:: https://img.shields.io/discord/327254708534116352.svg
    :target: https://adafru.it/discord
    :alt: Discord

.. image:: https://github.com/adafruit/Adafruit_CircuitPython_BLE_Magic_Light/workflows/Build%20CI/badge.svg
    :target: https://github.com/adafruit/Adafruit_CircuitPython_BLE_Magic_Light/actions
    :alt: Build Status

BLE service for Magic Light BLE RGB bulbs. Available from Amazon
`here <https://www.amazon.com/gp/product/B073S1KV4F>`_.


Dependencies
=============
This driver depends on:

* `Adafruit CircuitPython <https://github.com/adafruit/circuitpython>`_

Please ensure all dependencies are available on the CircuitPython filesystem.
This is easily achieved by downloading
`the Adafruit library and driver bundle <https://circuitpython.org/libraries>`_.

Installing from PyPI
=====================
.. note:: This library is not available on PyPI yet. Install documentation is included
   as a standard element. Stay tuned for PyPI availability!

On supported GNU/Linux systems like the Raspberry Pi, you can install the driver locally `from
PyPI <https://pypi.org/project/adafruit-circuitpython-ble_magic_light/>`_. To install for current user:

.. code-block:: shell

    pip3 install adafruit-circuitpython-ble-magic-light

To install system-wide (this may be required in some cases):

.. code-block:: shell

    sudo pip3 install adafruit-circuitpython-ble-magic-light

To install in a virtual environment in your current project:

.. code-block:: shell

    mkdir project-name && cd project-name
    python3 -m venv .env
    source .env/bin/activate
    pip3 install adafruit-circuitpython-ble-magic-light

Usage Example
=============

.. code:: python

    """This demo connects to a magic light and has it do a colorwheel."""
    import adafruit_ble
    import _bleio

    from adafruit_ble.advertising.standard import ProvideServicesAdvertisement
    from adafruit_ble_magic_light import MagicLightService

    # CircuitPython <6 uses its own ConnectionError type. So, is it if available. Otherwise,
    # the built in ConnectionError is used.
    connection_error = ConnectionError
    if hasattr(_bleio, "ConnectionError"):
        connection_error = _bleio.ConnectionError

    def find_connection():
        for connection in radio.connections:
            if MagicLightService not in connection:
                continue
            return connection, connection[MagicLightService]
        return None, None

    # Start advertising before messing with the display so that we can connect immediately.
    radio = adafruit_ble.BLERadio()

    active_connection, pixels = find_connection()
    current_notification = None
    app_icon_file = None
    while True:
        if not active_connection:
            print("Scanning for Magic Light")
            for scan in radio.start_scan(ProvideServicesAdvertisement):
                if MagicLightService in scan.services:
                    active_connection = radio.connect(scan)
                    try:
                        pixels = active_connection[MagicLightService]
                    except connection_error:
                        print("disconnected")
                        continue
                    break
            radio.stop_scan()

        i = 0
        while active_connection.connected:
            pixels[0] = colorwheel(i % 256)
            i += 1

        active_connection = None

Documentation
=============

API documentation for this library can be found on `Read the Docs <https://docs.circuitpython.org/projects/ble_magic_light/en/latest/>`_.

Contributing
============

Contributions are welcome! Please read our `Code of Conduct
<https://github.com/adafruit/Adafruit_CircuitPython_BLE_Magic_Light/blob/main/CODE_OF_CONDUCT.md>`_
before contributing to help this project stay welcoming.

Documentation
=============

For information on building library documentation, please check out `this guide <https://learn.adafruit.com/creating-and-sharing-a-circuitpython-library/sharing-our-docs-on-readthedocs#sphinx-5-1>`_.
