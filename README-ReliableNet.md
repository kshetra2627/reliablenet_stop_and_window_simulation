# ReliableNet

**Interactive Reliable Data Transfer Simulator: Stop-and-Wait and Sliding Window (Go-Back-N)**

ReliableNet is a browser-based Computer Networks mini-project. It simulates a sender and a receiver connected by an unreliable channel and shows, step by step, how data link layer protocols recover from lost frames and lost acknowledgements.

## Features

- **Two protocols:** Stop-and-Wait ARQ and Sliding Window (Go-Back-N).
- **Animated visualization:** frames and ACKs travel between the Sender and Receiver boxes. Lost packets stop mid-wire and turn into a red ✕.
- **Frame tracker:** every frame is colour-coded (grey = waiting, amber = in flight, green = acknowledged, red = lost). The sender window is outlined.
- **Live event log:** every send, loss, timeout, retransmission and ACK is recorded as it happens.
- **Statistics:** frames, total sent (including resends), lost, retransmissions, ACKs and round trips.
- **Manual control:**
  - **Pause / Resume** the simulation at any time.
  - **Step** through one event at a time.
  - **Lose next frame / Lose next ACK** to force a specific loss.
- **Compare protocols:** averages 300 runs of Stop-and-Wait and Sliding Window with the same settings.
- **Adjustable speed:** Slow, Normal, Fast.

## How to run

No installation or server is needed.

1. Download `reliablenet.html`.
2. Double-click it to open it in any modern browser (Chrome, Edge, Firefox, Safari).

The project is one self-contained file. An internet connection is only used for the Google Fonts; without it the page falls back to system fonts and works the same.

## Using the simulator

| Control | Meaning |
|---|---|
| Protocol | Stop-and-Wait, or Sliding Window (Go-Back-N) |
| Frames | Number of frames to send (1 to 20) |
| Loss % | Probability that any frame or ACK is lost (0 to 90) |
| Window | Sender window size (1 to 8). Only used by Sliding Window |
| Start transmission | Starts (or restarts) a simulation |
| Compare protocols | Runs both protocols 300 times each and shows the averages |
| Pause / Resume | Freezes or continues the animation |
| Step | Advances exactly one event (pauses the run first if needed) |
| Lose next frame | The next frame sent is dropped. Click repeatedly to queue more |
| Lose next ACK | The next ACK sent is dropped. Click repeatedly to queue more |
| Speed | Delay between events |

The pause, step and loss buttons are only active while a run is in progress.

## How it works

### Stop-and-Wait

1. The sender transmits one frame and waits.
2. If the frame arrives, the receiver sends an ACK and the sender moves to the next frame.
3. If the frame or its ACK is lost, the sender times out and retransmits the same frame.
4. If only the ACK was lost, the receiver recognises the duplicate frame, discards it and sends the ACK again.

Only one frame is outstanding at a time, so each attempt costs one round trip.

### Sliding Window (Go-Back-N)

1. The sender transmits up to **W** frames without waiting for ACKs.
2. The receiver accepts frames only in order. After a lost frame, later frames in the same window are discarded as out of order.
3. The receiver sends a cumulative ACK for the last in-order frame, and the window slides forward.
4. If nothing in the window is acknowledged (or the ACK is lost), the sender times out and goes back to the first unacknowledged frame, resending the window.

Each window round counts as one round trip, which is why Sliding Window usually needs far fewer round trips than Stop-and-Wait.

Stop-and-Wait is the special case of a window size of 1.

## Statistics explained

| Statistic | Meaning |
|---|---|
| Frames | Number of frames in the message |
| Sent (incl. resends) | Every frame transmission, including retransmissions |
| Lost | Frames and ACKs dropped by the channel |
| Retransmissions | Frames sent more than once |
| ACKs | Acknowledgements successfully received by the sender |
| Round trips | Stop-and-Wait: one per transmission attempt. Sliding Window: one per window round. Used as a measure of transmission time |

## Suggested demo

1. Set Frames = 6 and Loss % = 0, choose **Stop-and-Wait**, press **Start transmission**.
2. While frame 2 is travelling, click **Lose next frame** and watch the timeout and retransmission.
3. Click **Lose next ACK** during another frame to show the duplicate being discarded.
4. Switch to **Sliding Window** with Window = 4 and run it again. Use **Step** to walk through the window sliding.
5. Set Loss % to about 20 and press **Compare protocols** to show the difference in round trips.

## Simulation assumptions

- Loss is random and independent for each frame and each ACK, unless forced manually.
- Timeouts are simulated as events. There is no real-time timer or round-trip time model.
- The channel never reorders or corrupts frames. It only loses them.
- Sequence numbers do not wrap around.
- Selective Repeat is not implemented.

## Project structure

```
reliablenet.html    HTML, CSS and JavaScript in a single file
```

Inside the script:

- `gen(...)` is the protocol engine. It is a generator that produces one event at a time (send, loss, timeout, ACK), which is what lets the simulation be paused, stepped and interrupted by manual losses.
- `simARQ(...)` runs the engine to completion without animation, for the comparison feature.
- `play`-style functions (`show`, `tick`, `step1`) render events to the screen.

## Technologies

HTML5, CSS3 and vanilla JavaScript (no frameworks or libraries).
