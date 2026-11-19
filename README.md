# MemoryMaze

A maze game for memory-retention practice that you play with American Sign Language hand signs instead of a keyboard or touchscreen.

**Demo:** [video link — add after recording]

## Why

Memory-retention exercises are usually built for keyboard, mouse or touch input. That excludes people who communicate through sign language or who find fine motor input difficult. MemoryMaze makes hand signs the controller: a webcam watches your hand, a convolutional neural network recognizes the sign, and the sign moves your character through the maze. Finding the path from start to finish exercises spatial and working memory.

## How it works

```
webcam frame ──► resize to 200×200 ──► CNN classifier ──► sign (left / right / forward / back)
                                                                │
                                         collision check against maze walls
                                                                │
                                         move sprite and redraw maze ──► finish screen at the exit
```

1. **Capture.** OpenCV reads live frames from the default webcam.
2. **Recognize.** Each frame is resized to 200×200 and classified by a CNN built in Keras: three blocks of paired 3×3 convolutions (32, 64 and 128 filters), each followed by max pooling and dropout, then a dense layer and a softmax output. The four movement classes map to left, right, back and forward.
3. **Move with collision detection.** Before moving, the game crops the region of the maze where the sprite would land and thresholds it. If any wall pixels fall inside that region, the move is blocked and the sprite stays where it is.
4. **Render.** The sprite is drawn onto the maze using contour masking (threshold, find contours, build a mask, combine with the background) so only the character's outline is composited, not its rectangular background.
5. **Finish.** Reaching the exit region shows a finish screen.

## Training data

The recognition model was trained on 15,000+ hand images. Preprocessing used background subtraction and skeletonization to isolate the outline of the hand before classification.

<!-- Add: where the images came from, how many per class, and the train/test split. -->

## Results

<!-- Add measured numbers and how you got them, e.g.:
- Recognition accuracy: X% on a held-out test set of N images
- Latency: X ms per frame / X FPS on [hardware]
Only include figures you actually measured. -->

## Repository layout

| File | What it is |
|---|---|
| `finalMaze` | The complete game: webcam capture, sign recognition and the maze. **Run this one.** |
| `MergeCode` | An intermediate version that combined the recognizer with the maze (uses absolute local file paths). |
| `MazeCode` | The maze game on its own, before recognition was added. |

## Running it

**Requirements:** Python 3, a webcam.

```bash
pip install opencv-python tensorflow numpy
python finalMaze
```

`finalMaze` loads these files from the working directory. They are not in the repository yet, so add them before running:

- `weights.hdf5`: the trained model weights
- `Maze.png`, `accMaze.png` (start screen), `finishScreen.png`
- `spriteForward.png`, `spriteBackward.png`, `spriteLeft.png`, `spriteRight.png`

**Controls**

- Press any key on the start screen to begin.
- Make one of the four hand signs to the camera to move left, right, forward or back.
- Press `r` to reset to the start position.
- Press `Space` to stop the camera.

## Built with

Python, OpenCV, TensorFlow/Keras, NumPy.

## Author

Nishi Kaura — [github.com/nishikaura1](https://github.com/nishikaura1)
