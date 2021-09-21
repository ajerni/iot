# Andi's IOT Cockpit

This page interacts with Particle devices (Arduino based boards with WiFi) and is a learning example on Svelte.js and general use of Javascript and CSS as well.

## Technical info (Svelte + Vite)

This site is built with the Vite template for Svelte. It's using plain Javascript (Mainly Fetch API) to interact with Particle Photon boards.

https://svelte.dev/

https://vitejs.dev/

npm init vite@latest my-app -- --template svelte

## Summary

### Homepage can
- read Particle.variables with GET Request
- call Particle.functions with POST Request

### Particle devices can handle Events with:
- Particle.publish (send Events)
- Particle.subscribe (listen to Events)

### And the Homepage's equivalent to publishing and subscribing is
- POST Request an Event to Particle cloud API
- Use Javaspript native EventSource object to listen to published Events (eventSource.addEventListener)
