# Bardscan IIs – Teardown and Repair Notes

<img src="/img/bardscan-iis-1.jpg" width="900" />

<img src="/img/bardscan-iis-2.jpg" width="900" />

## Background

The Bardscan IIs is a portable 2D [mechanical sector](https://link.springer.com/chapter/10.1007/978-94-010-1103-7_29) [B-mode](https://assets.cambridge.org/97805217/57102/excerpt/9780521757102_excerpt.pdf) ultrasound intended for measuring bladder volume. 

The device was originally designed by Mediwatch PLC and released as the [Mediwatch Portascan](./pdf/mediwatch%20portascan%20-%20brochure.pdf), later upgrade to the Portascan+:

<img src="/img/mediwatch-portascan-1.jpg" width="900" />

<img src="/img/mediwatch-portascan-2.jpg" width="900" />

In 2008, Bard released a relabeled version as the [Bardscan II](./pdf/bardscan-iis-brochure.pdf). GE also released a relabeled version as the [GE Portascan](./pdf/ge%20portascan%20-%20user%20manual.pdf).

All three devices appear to share the same core design:
- battery-powered (with AC option)
- dual-frequency 3.5 / 5.0 MHz mechanical sector abdominal probe
- digital 512 × 512 × 8-bit scan converter
- ~8" color TFT touchscreen LCD (640x480)
- Windows environment

The differences between the Bardscan II and the Bardscan IIs are not clear. [This video](https://www.youtube.com/watch?v=pdQRYp7sCPQ) shows a teardown and repair of a Bardscan II, with the main PCB labelled "Mediwatch Portascan 2 Issue 2". My Bardscan IIs is labeled "Mediwatch Portascan OMAP Issue 6", and has a considerably simpler layout with much fewer components.

---

## Teardown

The main PCB, labeled "Mediwatch Portascan OMAP Issue 6":

<img src="/img/bardscan-iis-3.jpg" width="900" />

<img src="/img/bardscan-iis-4.jpg" width="900" />

<img src="/img/bardscan-iis-5.jpg" width="900" />

The touchscreen display is an [AU Optronics G084SN05-V9](./pdf/au%20optronics%20g084sn05-v9%20-%20datasheet.pdf):

<img src="/img/bardscan-iis-6.jpg" width="900" />

<img src="/img/bardscan-iis-7.jpg" width="900" />

The thermal printer PCB, labeled "Mediwatch Portascan+ Printer":

<img src="/img/bardscan-iis-8.jpg" width="900" />

The thermal printer is a [Fujitsu FTP-628MCL054](./pdf/fujitsu%20ftp-628mcl054%20-%20datasheet.pdf):

The ultrasound probe connects via a 16-pin [LEMO PFG.2B connector](https://www.lemo.com/int_en/solutions/originals/b-indoor-keyed.html):

<img src="/img/bardscan-iis-9.jpg" width="900" />

<img src="/img/bardscan-iis-10.jpg" width="900" />

<img src="/img/bardscan-iis-11.jpg" width="900" />

Which terminates at the PCB:

<img src="/img/bardscan-iis-12.jpg" width="900" />

<img src="/img/bardscan-iis-13.jpg" width="900" />

---

## Repair Notes

My ultrasound probe was not activating during a scan. The motor was not running and no image was being produced. The problem persisted on a second ultrasound probe.

An x-ray of the probes did not reveal any clear mechanical problem:

<img src="/img/bardscan-iis-14.jpg" width="900" />

<img src="/img/bardscan-iis-15.jpg" width="900" />

<img src="/img/bardscan-iis-16.jpg" width="900" />

<img src="/img/bardscan-iis-17.jpg" width="900" />

Two chips were overheating:

<img src="/img/bardscan-iis-18.jpg" width="480" />

<img src="/img/bardscan-iis-19.jpg" width="480" />

| Ref | Part | Function |
|-----|------|----------|
| U30 | LT3481 step-down switching regulator | Generates a low voltage rail used for the pulser |
| Q5 | ZXMN10A08E6TA N-Channel MOSFET N-CH 100V 1.5A | Driven by U8, drain goes to D7, acts as high-voltage enable when a probe is present |

Testing Q5 revealed it was blown. Replacing Q5 eliminated the overheating problem, but the probes were still not activating.

After a lot of oscilloscope and multimeter probing, the rough layout of the PCB becomes clear:

<img src="/img/bardscan-iis-20.jpg" width="900" />

After investigation, more parts needed replacing:

| Ref | Part | Function |
|-----|------|----------|
| U7 | TC6320TG-G high-voltage complementary MOSFET pair (1x N channel, 1x P-channel) | Ultrasound output switch |
| U8 | LM5112 MOSFET | high-voltage enable gate |
| D5 and D6 | Schottky diode | Shapes the high-voltage rail and protects the pulser |

After replacement, signals were checked higher up the signal path:

| Ref | Part | Function |
|-----|------|----------|
| U6 | MD1213 high-speed dual MOSFET driver | High-voltage gate driver for U7 |

These signals looked good, everything was put back together, and the probes were working again.

---