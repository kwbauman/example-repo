# RNDIS Link Speed Change Notification

## Overview

In RNDIS (Remote Network Driver Interface Specification), Windows is notified of link speed changes through the device sending a **REMOTE_NDIS_INDICATE_STATUS_MSG** message to the host.

## Mechanism

### 1. Status Indication Message

When the link speed changes, the RNDIS device sends an indication message to the Windows host with the following structure:

```
REMOTE_NDIS_INDICATE_STATUS_MSG
```

### 2. Status Code

The message includes a status code field set to:

```
RNDIS_STATUS_LINK_SPEED_CHANGE (0x40010013)
```

This status code specifically indicates that the link speed has changed and Windows should query the new speed.

### 3. Message Structure

The REMOTE_NDIS_INDICATE_STATUS_MSG contains:

- **MessageType**: 0x00000007 (REMOTE_NDIS_INDICATE_STATUS_MSG)
- **MessageLength**: Total length of the message
- **Status**: 0x40010013 (RNDIS_STATUS_LINK_SPEED_CHANGE)
- **StatusBufferLength**: Length of status buffer (typically 0 for link speed change)
- **StatusBufferOffset**: Offset to status buffer (typically 0)

### 4. Windows Response

Upon receiving this notification:

1. Windows recognizes the RNDIS_STATUS_LINK_SPEED_CHANGE status
2. Windows queries the device for the new link speed using OID_GEN_LINK_SPEED or OID_GEN_LINK_SPEED_EX
3. Windows updates its internal network adapter state with the new speed
4. Applications and the network stack are notified of the speed change

## Implementation Notes

- The device must send this indication whenever the physical link speed changes (e.g., switching from 100Mbps to 1Gbps)
- The indication can be sent over the interrupt endpoint for USB RNDIS devices
- Windows will automatically query for the new speed; the device should be ready to respond to OID queries
- This mechanism ensures the Windows network stack maintains accurate link speed information

## Related Status Codes

Other related RNDIS status indications:
- `RNDIS_STATUS_MEDIA_CONNECT` (0x4001000B): Link is up
- `RNDIS_STATUS_MEDIA_DISCONNECT` (0x4001000C): Link is down
- `RNDIS_STATUS_MEDIA_SPECIFIC_INDICATION` (0x40010012): Media-specific indication

## References

- Microsoft RNDIS Specification
- USB Communications Device Class (CDC) Specification
- NDIS Status Indications Documentation
