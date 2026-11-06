# ProPresenter to Daslight: Network MIDI Setup

The Macs are on different subnets (PPMac on `192.168.1.x`, Mini on `192.168.4.x`) but connected to the same unmanaged switch. Give each Mac a second IP address on a shared private subnet. This does not require the router password.

Both Macs have Wi-Fi and Ethernet turned on. PPMac needs internet because it sends lyrics, sermon slides, and other content to the PDC for live streaming. The `MIDI` service has no router and sits at the bottom of the service order, so it only handles traffic to `10.10.10.x`. **Leave PPMac's Wi-Fi and Ethernet on.**

## 1. Record the current addresses

In **System Settings > Network**, open **Wi-Fi** and **Ethernet** on each Mac and write down their IP addresses. This gives you a baseline and shows which connection is on which subnet.

## 2. Add a second network service on each Mac

1. Go to **System Settings > Network**.
2. Click the **...** button below the list of services, then choose **Add Service**.
3. Set **Interface** to the Ethernet connection plugged into the switch, not Wi-Fi. Name it `MIDI` and click **Create**.
4. Open the new `MIDI` service and click **Details > TCP/IP**.
5. Set **Configure IPv4** to **Manually**, enter the values below, and leave **Router** blank.

| | IP address | Subnet mask |
|---|---|---|
| **PPMac** | `10.10.10.1` | `255.255.255.0` |
| **Mini** | `10.10.10.2` | `255.255.255.0` |

## 3. Keep internet on the existing connection

Click **...** again and choose **Set Service Order**. Drag `MIDI` to the bottom of the list on both Macs, then click **OK**.

## 4. Test the connection

On PPMac, run in Terminal:

```
ping -c 4 10.10.10.2
```

On the Mini, run in Terminal:

```
ping -c 4 10.10.10.1
```

If either ping fails, first confirm the Mac is actually wired to the switch and not just using Wi-Fi. If it is, temporarily turn off the macOS firewall on both Macs and test again.

## 5. Reconnect MIDI

1. On PPMac, open **Audio MIDI Setup > Window > Show MIDI Studio**, then double-click **Network**.
2. Make sure a session is enabled on both Macs.
3. On PPMac, click **+** under **Directory** and add the Mini: use any name, host `10.10.10.2`, and port `5004`.
4. Select that entry and click **Connect**. Do not use the entry that appeared automatically. Connecting to `10.10.10.2` keeps MIDI from using the Wi-Fi route or the old entry.

## 6. Test PPMac before relying on it

1. Confirm that a website loads.
2. Run this in Terminal:

   ```
   route -n get default
   ```

   The `interface` and `gateway` should match the connection PPMac was using for internet, and the gateway should not be a `10.10.10.x` address.
3. Send lyrics or slides to the PDC and confirm they appear on the stream computer.
4. Send a MIDI cue and confirm Daslight responds.

If both pings work but the cue does not trigger Daslight, the network is fine and the problem is in the MIDI settings in ProPresenter or Daslight.

## To undo the change

Select the `MIDI` service in **System Settings > Network**, click **...**, and choose **Delete Service**. PPMac's Wi-Fi and Ethernet settings will not change.
