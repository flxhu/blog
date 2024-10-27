---
title: "Hooking up a DC motor with a Quadrature Encoder"
date: 2024-10-27
tags:
- robotics
- arduino
---

The mobile robot [needs to know where it is](https://www.youtube.com/watch?v=bZe5J8SVCYQ).
A good source of data for inferring location information are
sensors that measure wheel revolutions.

Fortunately there is a great selection of DC motors with gearboxes
and hall sensors available from sites like Aliexpress. They typically look
like this: 

<img src="/blog/assets/2023-10-27-quadencoder/motorback.avif" width="50%">

The pinout is straightforward:
1. two connectors for motor power
2. 5V or 3.3V power for the sensor
3. two digital data lines for the hall sensor data

<img src="/blog/assets/2023-10-27-quadencoder/quadpinout.avif" width="30%">

The sensor operates as follows:
1. there is a magnet attached to the motor axis that triggers
2. two hall sensors placed 90° apart. Note the two little knobs in the first
image.

When the motor is spinning, the magnet moves along the two hall sensors
and triggers them at different times. This gives a shifted rate signal
on the two data lines:

![Quadrature](/blog/assets/2023-10-27-quadencoder/quadrature.webp "DC motor with gearbx and hall sensor")

This is called a quadrature encoder 
(an alternative would be a [linear encoder](https://en.wikipedia.org/wiki/Incremental_encoder) for example).

From the signal you can deduce three things:
1. revolutions of the motor by counting signal transitions
2. RPM by measuring time between transitions
3. direction of rotation by comparing the two signals

The direction becomes obvious of you look at the schema. In one direction
the reading of the second sensor heads the change of the first, in the
other direction it trails it. For Arduino, the code is as simple as this:
```
    PinStatus valueA = digitalRead(pin1_);
    PinStatus valueB = digitalRead(pin2_);

    int direction = valueA == valueB ? 1 : -1;
    count_ += direction;
```

You can either hook it up to an interrupt on one of the pins, so that
each revolution triggers it, or poll the pins and detect changes
yourself (of course you need to keep the previous state then). Together
with measuring time delta, you get RPM.


