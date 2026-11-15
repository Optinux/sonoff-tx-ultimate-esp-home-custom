# sonoff-tx-ultimate-esp-home-custom

My ESPHome setup for a Sonoff TX Ultimate, built on [TX-Ultimate-Easy](https://github.com/edwardtfn/TX-Ultimate-Easy)

## What it does

- **Tap** → toggles the ceiling light (Deckenlicht)
- **Hold ~1.25 s**, while still touching, any number of fingers → beep + toggles the room lighting (Raumbeleuchtung)
- Multi-finger taps count as taps. Swipes do nothing.

Home Assistant does the switching: one automation listens to the `click_event` entity and the events `touch/multi_tap` and `touch/hold`.

## What's different from the official config

- A hold timer that fires *while you're still touching* (upstream's long press only fires on release)
- A beep at the hold, plus custom HA events for hold and multi-finger tap
- Tap threshold 600 ms (default 500) and multi-click delay 0 (default 250), so taps react right away
- A fix so long touches also stop the timer (upstream only logs them)

## Why this lives in the ESPHome config

- HA only sees short pulses *after* release, so it can't know you're still holding. The timer has to run on the device.
- Multi-finger touches arrive as one "multi-touch" frame on release, with no duration, so upstream never produces a long press for them. A timer started at touch-down doesn't care how many fingers there are.
- Instant click mode fires on touch-down, so it can't tell a tap from a hold. In v2026.9.3 it was also silently ignored (a bug), so this uses normal mode.
- Events instead of state pulses, because a pulse sensor stopped publishing after its first use.

## Notes

- Set the **Button multi-click delay** number to `0` in HA once.
- Enable **Allow the device to perform Home Assistant actions** for the device, or HA won't receive the events.
- Keep `hold_time` ≥ the tap threshold.
- It relies on upstream internals, so pin `ref:` to a version tag.
