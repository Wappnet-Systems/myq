<SPAN ALIGN="CENTER" STYLE="text-align:center">
<DIV ALIGN="CENTER" STYLE="text-align:center">

[![myQ: A modern implementation of the myQ API for Liftmaster and Chamberlain devices](https://raw.githubusercontent.com/hjdhjd/myq/main/myQ.svg)](https://github.com/hjdhjd/myq)

# myQ

[![Downloads](https://img.shields.io/npm/dt/@hjdhjd/myq?color=%235EB5E5&logo=icloud&logoColor=%23FFFFFF&style=for-the-badge)](https://www.npmjs.com/package/@hjdhjd/myq)
[![Version](https://img.shields.io/npm/v/@hjdhjd/myq?color=%235EB5E5&label=myQ&logoColor=%23FFFFFF&style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyByb2xlPSJpbWciIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgdmlld0JveD0iMCAwIDI0IDI0Ij48cGF0aCBzdHlsZT0iZmlsbDojRkZGRkZGIiBkPSJNMjMuOTkzIDkuODE2TDEyIDIuNDczbC00LjEyIDIuNTI0VjIuNDczSDQuMTI0djQuODE5TC4wMDQgOS44MTZsMS45NjEgMy4yMDIgMi4xNi0xLjMxNXY5LjgyNmgxNS43NDl2LTkuODI2bDIuMTU5IDEuMzE1IDEuOTYtMy4yMDIiLz48L3N2Zz4K)](https://www.npmjs.com/package/@hjdhjd/myq)

## myQ: A modern implementation of the myQ API for Liftmaster and Chamberlain devices.
</DIV>
</SPAN>

`myq` is a library that enables you to connect to and communicate with [myQ-enabled devices](https://myq.com). myQ-enabled devices include many garage door openers made primarily by Liftmaster, Chamberlain, and Craftsman, but includes other brands as well. You can determine if your garage door or other device is myQ-enabled by checking the [myQ compatibility check tool](https://www.myq.com/myq-compatibility) on the [myQ](https://www.myq.com) website.

## Why use this library for myQ support?
In short - because I use it every day to support a very popular [Homebridge](https://homebridge.io) plugin named [homebridge-myq](https://www.npmjs.com/package/homebridge-myq) that I maintain. I have been occasionally asked if I would consider packaging the core API library separately from the plugin so that other open source projects can take advantage of the work that's been done here to understand and decode the myQ API.

In addition, this implementation is unique: it's the first complete open source implementation of the latest myQ API, v6. The v6 API is quite different in significant ways, including a shift to OAuth-based authentication that's clearly the path the myQ API is moving toward in the future. Additionally, v6 brings other advantages besides leveraging modern OAuth semantics, such as making devices shared across accounts available, which has previously been unavailable in prior myQ API versions.

Finally - the most significant reason that you should use this library: it's very well-tested, it is modern, and most importantly, *it just works*. It's quite easy to add support for myQ in your project using this library, and you can rely on the fact that the code is used by a significant population of users out there who ensure its continued robustness.

### <A NAME="myq-contribute"></A>How you can contribute and make this library even better
This implementation is largely feature complete. It doesn't support myQ locks or cameras, but may do so in time, though contributions are always welcome.

The myQ API is undocumented and implementing a library like this one is the result of many hours of trial and error as well as community support. This work stands on the shoulders of other myQ API projects out there and this project attempts to contribute back to that community base of knowledge to further improve myQ support for everyone in the ecosystem.

### Features
- Full access to the myQ devices JSON.
- The ability to retrieve the status of any supported myQ device.
- The ability to open or close a supported garage door.
- The ability to turn on or off a supported lamp.

## Changelog
* [Changelog](https://github.com/hjdhjd/myq/blob/main/docs/Changelog.md): changes and release history of this library.

## Installation
To use this library in Node, install it from the command line:

```sh
npm install @hjdhjd/myq
```

## Documentation

If you'd like to see all this in action in a well-documented, real-world example, please take a good look at my [homebridge-myq](https://github.com/hjdhjd/homebridge-myq) project. It relies heavily on this library for the core functionality it provides.

### myQApi(log: myQLogging)
Initialize the myQ API. `log` is an optional parameter that enables you to customize the type of logging that can be generated, including debug logging. If `log` isn't specified, the myQ API will default to logging to the console.

### login(email: string, password: string)
Login to the myQ API using the myQ account information contained in `email` and `password` and call `refreshDevices()` to initialize the API and populate the list of myQ devices associated with a given account.

**Note: `login()` must be called before any other API function can be used.**

Returns: `true` if successful, `false` otherwise.

### refreshDevices()
This is where the magic happens. This function:

* If we do have an access token and it's nearly time to refresh it, it will do so.
* It then requests a refresh of the myQ state and device information for all the myQ devices associated with the currently logged in account. There are failsafes in place to ensure it can't be called more than once every two seconds in order to prevent overtaxing the myQ API and potentially lockout an account.

Returns: `true` if successful, `false` otherwise.

### myQApi.devices[]
The devices property maintains the list of all known myQ devices. It is an array of `myQDevice` objects, and you can look through [myq-types.ts](https://github.com/hjdhjd/myq/blob/main/src/myq-types.ts) for a sense of what's contained in a `myQDevice` object.

This property is refreshed each time `refreshDevices()` is called.

### execute(device: myQDevice, command: string)
Execute a command on a given myQ device. Valid values for `command`:

  * Garage doors: `open` and `close`
  * Lamps: `on` and `off`

Returns: `true` if successful, `false` otherwise.

### getDevice(serial: string)
Get the details of a specific device identified by the serial number `serial` in the myQ device list. In practice, I rarely use this, and I suspect most people won't either, in favor of walking the entire myQ device list which is what most people want to do most of the time.

Returns: `myQDevice` if found, or `null`.

### getDeviceName(device: myQDevice)
Given `device`, returns a nicely formatted device string suitable for logging information or end users.

Returns: a string representing the device name, model, and serial number, if available.

### getHwInfo(serial: string)
Get the model information of a device identified by the serial number `serial`. myQ devices have a specific serial number pattern, and you can use it to deduce the model information of a particular device.

Returns: `myQHwInfo` if found, or `null` if we can't deduce what the hardware and model information is.

## Library Development Dashboard
This is mostly of interest to the true developer nerds amongst us.

[![License](https://img.shields.io/npm/l/@hjdhjd/myq?color=%230559C9&logo=open%20source%20initiative&logoColor=%23FFFFFF&style=for-the-badge)](https://github.com/hjdhjd/myq/blob/main/LICENSE.md)
[![Build Status](https://img.shields.io/github/actions/workflow/status/hjdhjd/myq/ci.yml?branch=main&color=%230559C9&logo=github-actions&logoColor=%23FFFFFF&style=for-the-badge)](https://github.com/hjdhjd/myq/actions?query=workflow%3A%22Continuous+Integration%22)
[![Dependencies](https://img.shields.io/librariesio/release/npm/@hjdhjd/myq?color=%230559C9&logo=dependabot&style=for-the-badge)](https://libraries.io/npm/@hjdhjd/myq)
[![GitHub commits since latest release (by SemVer)](https://img.shields.io/github/commits-since/hjdhjd/myq/latest?color=%230559C9&logo=github&sort=semver&style=for-the-badge)](https://github.com/hjdhjd/myq/commits/main)
