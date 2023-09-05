---
layout: page
title: IOT Project - Medicine-Guardian
description: FIT/IOT Lab Wireless
img: assets/img/projects/IOT/iot_wireless.png
importance: 2
pdf: iot_wireless_project.pdf
category: work
giscus_comments: true
---

# Overview

In recent years, with the development of IoT techniques and industrial automation, using sensors to capture the environment and connecting things with networks are common and popular ways to make industrial processes safe and efficient, to make our lives convenient. Abundant IoT applications are implemented in various kinds of scenarios, here in this project, I propose one IoT application working on the safety of sensitive cargo during transportation.

## Definition and introduction of the assumed IoT application

Food and medical supplies often require temperature and humidity control during storage and transportation. In order to facilitate control, logistics companies use sealed containers with environmental controls. These containers are equipped with sensors that will send status reports to the central network to monitor these containers to control humidity and temperature. If there is a problem with the environment inside the container, if a leak in the sealed container is found, the sensor will immediately send an alarm to the central network to take appropriate measures. The use of the Internet of Things can reduce the deterioration of sensitive goods and prevent pollution.

It's a very important application according to the scope of using, and also a very prospective application for different levels of security. For example, because insulin drugs have higher requirements for the delivery environment than ordinary drugs, UNOPENED insulin is best stored inside the fridge [2° to 8°Celcius (36° to 46°Fahrenheit)]. Heat makes insulin break down and will not work well to lower your blood sugar. Also, if insulin is frozen(lower than 0°Celcius), do not use even after thawing. Because freezing temperature will break down the insulin and then it will not work well to lower your blood sugar. Then the entire process needs to be accurately controlled at 2-8°C, and real-time temperature monitoring is critical. Insulin is also very sensitive to sunlight, indoor lights.


Another example is the COVID-19 VACCINE by Pfizer. The vaccine transport, storage and continuous temperature monitoring have extreme constraints. For example, there are three options for storage: ultra-low-temperature freezers, which are commercially available and can extend shelf life for up to six months; the Pfizer thermal shippers, in which doses will arrive, that can be used as temporary storage units by refilling with dry ice every five days for up to 30 days of storage; refrigeration units that are commonly available in hospitals. The vaccine can be stored for five days at refrigerated 2-8°C conditions.

To be more precise and describable, we set the scenario as a medical drug delivery vehicle in motion, where the drug need to be maintained at a strict interval of temperature and several other conditions, which we will talk about later in the architecture. The IoT application is named *Medicine-Guardian*.

# Architecture of the System

## The functions implemented

Here I provide several automation functions of *Medicine-Guardian*, which implement three different scenarios for several medicines on the delivery vehicle: 

**1) Collecting light information and calling the alarm when the light is on. Once we get the signal of alarm for light, switch the states of lights(turn off the light).**

Corresponding scenario: Supposing that there is one drug that need one strict light constraint of darkness, it cannot suffer from lightness.

**2) Collecting temperature information information. If the temperature is >8°C or <2°C, turn on the heater(temperature controller) until the temperature arrives 5.** 
However, if there is the temperature is extremely low or high (<0°C or > 30°C), or no heater could be used, send alarm to the monitor. To make this scenario more interesting, we will add more details for the heater at the part of implementation.

Corresponding scenario: Supposing that there is one drug that the best storage temperature is between 2 and 8. If the temperature is lower than 0 or higher than 30, the drug will be useless.

**3) Collecting accelerator information, and sending alarm if accelerator sensor has a great value or it makes a great change in short time period.**

Corresponding scenario: Supposing that there is one drug that cannot accept the shaking, we need to avoid the possible violent variation of velocity.

## Servers and clients installed

In this project, we set two kinds of clients: on the front-end and on the sensor node. To realise the communication, we also need one server on the sensor node. In the experiment, we reserve some M3 nodes on the Grenoble site, and set them up with flashing two firmwares nodes. Also, to compare the difference between HTTP and COAP, we can access nodes over HTTP over IPv6 from the SSH frontend by using a Contiki tunslip6 bridge and launch HTTP server or launch requests on a CoAP Contiki Erbium server implementation.

# Demo of Medicine-Guardian

To give a full view of the demo, we offer the code of the CoAP server/client and the HTTP server/client and other files needed.

You could find more details in the Report PDF.
