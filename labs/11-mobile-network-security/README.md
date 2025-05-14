# Mobile Network Security

Concern on mobile network security refers to cellular network communications (at all levels, not necessarily data transfers) and actual application networking data (that can happen on top of both cellular network and WiFi, potentially also including Bluetooth).

For this lab we will use [mitmproxy](https://mitmproxy.org/) to analyze traffic from and to a mobile device (be it Android, iOS).
mitmproxy can capture HTTPS traffic, log it and provide it to the user / security analyst.
Consider it as a [`tcpdump`](https://www.tcpdump.org/) interface for mobile devices and applications.

## Task 0

mitmproxy is your Swiss-army knife for debugging, testing, privacy measurements, and penetration testing.
It can be used to intercept, inspect, modify and replay web traffic such as HTTP/1, HTTP/2, WebSockets, or any other SSL/TLS-protected protocols.
You can prettify and decode a variety of message types ranging from HTML to [Protobuf](https://protobuf.dev/), intercept specific messages on-the-fly, modify them before they reach their destination, and replay them to a client or server later on.

Install mitmproxy for your machine from [this link](https://mitmproxy.org/).

### iOS

Follow [this tutorial](https://medium.com/testvagrant/intercept-ios-android-network-calls-using-mitmproxy-4d3c94831f62) in order to set up your iPhone.

### Android

Follow [this tutorial](https://medium.com/testvagrant/intercept-ios-android-network-calls-using-mitmproxy-4d3c94831f62) in order to set up your Android device.
Alternative resources can be found [here](https://medium.com/@williamxyz/monitoring-network-on-android-with-mitmproxy-bfb8722191ee).

## Task 1

Follow [this tutorial](https://gaikwadchetan93.medium.com/monitoring-modifying-android-app-network-traffic-via-mitmproxy-part-2-7128c0e1ce3e) in order to get familiarized with mitmproxy.

## Task 2

Follow [this tutorial](https://itnext.io/how-to-record-replay-http-traffic-in-android-and-ios-apps-db24a5dcc0e) in order to change the Wikipedia page response.
How can you match a https request with a custom response every time?

> [!NOTE]
> Use the `replay` option.

## Task 3

Follow [this tutorial](https://approov.io/blog/how-to-mitm-attack-the-api-of-an-android-app) in order to find how to MitM Attack the API of an Android App.

## Other Resources

- [Read and Manipulate Network Traffic on Android with mitmproxy](https://www.youtube.com/watch?v=c4wS9n7yilA&ab_channel=media.ccc.de)
- [Mobile iOS Development with Mitmproxy](https://rafellk.medium.com/mobile-development-with-mitmproxy-mocking-stuff-is-sooo-cool-6d9e2a98f1ea)
