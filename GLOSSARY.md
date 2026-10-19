# SapkaLink

SapkaLink concerns the relationship between an Android phone using Link to Windows and an independent Linux desktop client.

## Language

**Link to Windows**:
Microsoft's existing Android phone software that the proposed Linux client must interoperate with.
_Avoid_: Our companion app, generic Android bridge

**Phone Link**:
Microsoft's Windows desktop application, which provides a reference for some phone integration features.
_Avoid_: The entire connected-camera stack

**Connected camera**:
Microsoft's feature that makes a linked phone's camera available to Windows applications, configured through Windows Mobile devices settings.
_Avoid_: Screen mirror, Phone Link preview

**Pairing**:
An explicitly authorized association between the user's phone and a desktop client.
_Avoid_: A video connection, credential copying

**Connection**:
A currently usable communication relationship with an already paired phone.
_Avoid_: Pairing, camera session

**Camera session**:
A user-authorized period in which the phone supplies camera images to the desktop client.
_Avoid_: Persistent camera permission, screen-sharing session

**Virtual webcam**:
A desktop camera source that another application can discover, select, and use for live video.
_Avoid_: Preview window, video file, screen-share source

**Compatibility evidence**:
Version-scoped observations demonstrating interoperability with the existing Microsoft phone stack.
_Avoid_: A functional alternative, a plausible protocol description
