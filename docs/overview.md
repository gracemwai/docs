# Probe


## Automated Battery Testing & Reuse Platform
<p class="probe-lead">
Probe is a cloud-connected battery testing and inventory platform designed to help recyclers assess used lithium-ion batteries and connect batteries suitable for reuse with UPS companies.
</p>


By combining **IoT-enabled battery testing, a backend API, inventory management, and booking workflows**, Probe creates a bridge between battery recyclers and organizations looking for reliable second-life battery sources.




<div class="probe-buttons" style="justify-content: flex-start;">
  <a href="./hardware" class="probe-button primary">Explore the Hardware →</a>
  <a href="./backend/overview" class="probe-button primary">Explore the Backend →</a>
</div>

## Platform Highlights

<div class="probe-features probe-features-3col">
  <div class="probe-card">
    <img src="/images/recycler-landing-page.jpg" alt="Recycler landing page" class="probe-screenshot">
    <h3>Recycler Landing Page</h3>
  </div>
  <div class="probe-card">
    <img src="/images/device-registry.jpg" alt="Battery registry" class="probe-screenshot">
    <h3>Battery Registry</h3>
  </div>
  <div class="probe-card">
    <img src="/images/live-data.jpg" alt="Live battery testing data" class="probe-screenshot">
    <h3>Live Testing Data</h3>
  </div>
  <div class="probe-card">
    <img src="/images/ups-landing-page.jpg" alt="UPS company landing page" class="probe-screenshot">
    <h3>UPS Landing Page</h3>
  </div>
  <div class="probe-card">
    <img src="/images/booking.jpg" alt="Booking page" class="probe-screenshot">
    <h3>Booking</h3>
  </div>
  <div class="probe-card">
    <img src="/images/booking-confirmation.jpg" alt="Booking confirmation page" class="probe-screenshot">
    <h3>Booking Confirmation</h3>
  </div>
</div>


## What is Probe?

Probe provides a structured workflow for taking a used battery from **physical testing to potential reuse**.

A recycler places a battery into the Probe testing hardware. The connected sensors capture electrical and environmental measurements, including:

<div class="probe-tags">
  <span class="probe-tag">Temperature</span>
  <span class="probe-tag">Resting voltage</span>
  <span class="probe-tag">Load voltage</span>
  <span class="probe-tag">Current</span>
</div>

The IoT device transmits these measurements to the backend using **MQTT**. The backend validates and processes the incoming telemetry, calculates the battery's **State of Health (SoH)**, and stores the resulting information for use across the platform.

The tested battery can then be managed as part of the recycler's inventory. UPS companies can browse suitable batteries and submit booking requests through the platform.


## The Problem It Solves
Battery recycling and redistribution often depend on manual testing, inventory tracking, and communication between recyclers and organizations looking for battery stock.


Probe brings these processes into one connected platform, making it easier to test, manage, and reuse suitable batteries.


It enables:


Recyclers to register, test, and manage battery inventory.
UPS companies to discover available batteries and submit booking requests.
Users to track bookings through a defined status lifecycle.
Connected hardware to transmit battery readings to the platform for processing and monitoring.


This creates a clearer workflow from battery testing → assessment → inventory → reuse.


## Who Uses Probe

<div class="probe-role-box">

**Recycler**
Tests batteries, registers battery assets, manages inventory, and views testing data.

</div>

<div class="probe-role-box">

**UPS Company**
Browses available batteries, views battery information, and submits booking requests.

</div>

<div class="probe-role-box">

**Admin**
Manages users, oversees the platform, and accesses administrative functionality.

</div>

## Key Features

- **Authentication** — Signup, login, password reset, and role-based access using JWT.
- **Battery Management** — Register, search, filter, and manage battery assets.
- **IoT Testing** — Capture battery measurements through connected hardware and MQTT.
- **Inventory Management** — Track available battery stock and its status.
- **Booking Management** — Create, update, and track battery bookings through defined statuses.
- **Dashboards** — Provide users with access to inventory, battery data, and booking functionality.

## Explore Probe

**Want to understand how Probe works?**
Start with the system architecture and follow the journey from physical battery testing to the digital platform.

<a href="./architecture" class="probe-button primary">Start with the Architecture →</a>