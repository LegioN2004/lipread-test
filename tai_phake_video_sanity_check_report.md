# Tai Phake Video Sanity Check Report

## Purpose

This report records the results from running `tai_phake_video_sanity_check.ipynb` against the videos currently selected by the notebook.

The notebook is a preprocessing and data-quality check for a LipNet-style video pipeline. It does not train a model or determine whether the mouth is correctly located.

## Dataset layout used

The notebook searched this layout:

```text
data/<speaker>/<digit>/<digit>.mp4
```

Examples:

```text
data/s1/d0/d0.mp4
data/s1/d1/d1.mp4
data/s24/d0/d0.mp4
data/s24/d10/d10.mp4
```

In this run, the notebook was configured to include the speaker directories `s1` and `s24`, and digit directories `d0` through `d10`.

## Videos used

The discovery cell found 13 videos:

| # | File | Speaker | Digit | Frames | Duration (seconds) |
|---:|---|---|---|---:|---:|
| 1 | `data/s1/d0/d0.mp4` | s1 | d0 | 33 | 1.100 |
| 2 | `data/s1/d1/d1.mp4` | s1 | d1 | 32 | 1.067 |
| 3 | `data/s24/d0/d0.mp4` | s24 | d0 | 30 | 1.000 |
| 4 | `data/s24/d1/d1.mp4` | s24 | d1 | 31 | 1.033 |
| 5 | `data/s24/d10/d10.mp4` | s24 | d10 | 30 | 1.000 |
| 6 | `data/s24/d2/d2.mp4` | s24 | d2 | 39 | 1.300 |
| 7 | `data/s24/d3/d3.mp4` | s24 | d3 | 28 | 0.933 |
| 8 | `data/s24/d4/d4.mp4` | s24 | d4 | 37 | 1.233 |
| 9 | `data/s24/d5/d5.mp4` | s24 | d5 | 33 | 1.100 |
| 10 | `data/s24/d6/d6.mp4` | s24 | d6 | 34 | 1.133 |
| 11 | `data/s24/d7/d7.mp4` | s24 | d7 | 31 | 1.033 |
| 12 | `data/s24/d8/d8.mp4` | s24 | d8 | 31 | 1.033 |
| 13 | `data/s24/d9/d9.mp4` | s24 | d9 | 31 | 1.033 |

## Results

### 1. Video opening and metadata

- Total videos: **13**
- Readable videos: **13**
- Unreadable videos: **0**
- FPS: **30.0 for all 13 videos**
- Reported dimensions: **720 width x 1280 height for all 13 videos**
- Duration range: **0.933 to 1.300 seconds**
- Mean duration: **1.077 seconds**
- Frame count range: **28 to 39 frames**

### 2. Unusual-video checks

The FPS check found no videos outside the 29.5–30.5 FPS range.

The resolution check displayed all 13 videos because the notebook checks specifically for `1280x720`, while OpenCV reported these files as `width=720` and `height=1280`. This means the files appear to be consistently portrait-oriented, not that they have inconsistent resolutions.

The notebook should therefore describe the expected format as `720x1280` when using OpenCV metadata, unless the video orientation is intentionally rotated before processing.

### 3. Frame decoding

- Videos with bad frames: **0**
- Videos where expected and decoded frame counts differed: **0**
- Every selected video decoded successfully from beginning to end.

### 4. Grayscale and TensorFlow preprocessing

The notebook successfully performed the following operations:

1. Read video frames with OpenCV.
2. Converted BGR frames to RGB.
3. Converted RGB frames to grayscale.
4. Converted the frame sequence to a TensorFlow `float32` tensor.
5. Normalized the tensor using its mean and standard deviation.

The sample video was `data/s1/d0/d0.mp4`:

- Processed tensor shape: `(33, 1280, 720)`
- Data type: `float32`
- Minimum normalized value: approximately `-2.363`
- Maximum normalized value: approximately `1.855`
- Mean: approximately `0`
- Standard deviation: approximately `1`

The shape is `(frames, height, width)`, so it confirms the portrait orientation reported by OpenCV.

### 5. Example crop

The notebook tested a proportional crop on the sample video rather than using the GRID coordinates. The result was:

- Original shape: `(33, 1280, 720)`
- Example cropped shape: `(33, 576, 360)`

This confirms that array cropping works. It does **not** confirm that the crop contains the speaker's mouth.

### 6. Entire-dataset preprocessing

- Successful videos: **13**
- Failed videos: **0**

Every selected video produced a normalized grayscale TensorFlow sequence without an exception.

## What this proves

For the 13 selected clips, the raw video files can be opened, decoded, converted to grayscale, normalized, and represented as TensorFlow tensors. The files are also consistent at 30 FPS and share the same reported resolution.

## What this does not prove

This run does not prove that:

- the mouth is visible in every frame;
- the example crop contains the mouth;
- the crop is consistent across speakers;
- the labels are correct beyond the directory names;
- the dataset is large enough for training;
- the videos are suitable for successful LipNet training;
- the portrait orientation is appropriate for the intended model input.

The largest remaining task is selecting or detecting a reliable mouth region for these Tai Phake recordings. The notebook's proportional crop is only a technical crop test and should not be treated as a final mouth crop.

## Why the original notebook was probably generated

The original request appears to have been about checking whether a custom Tai Phake video dataset could be used with a LipNet-style tutorial. The notebook was therefore generated as a **sanity-check notebook** before model training. Its purpose was to answer basic pipeline questions first:

- Can the files be found?
- Can OpenCV read them?
- Are FPS and dimensions consistent?
- Are frames corrupted?
- Can the frames be converted and normalized?
- Can a crop be applied without errors?

That explains why the notebook focuses on file discovery and preprocessing rather than prediction or model training. It was not sufficient by itself to solve the complete lip-reading task because automatic mouth localization, labels, sequence preparation, model architecture, and training evaluation were outside its scope.

## Short summary for ChatGPT

> I ran `tai_phake_video_sanity_check.ipynb` on 13 videos found under `data/s1` and `data/s24`, using the layout `data/<speaker>/<digit>/<digit>.mp4`. All 13 videos opened successfully, all were 30 FPS, all reported `720x1280` in OpenCV metadata, no bad frames were found, and all decoded and normalized successfully as grayscale TensorFlow sequences. The sample sequence had shape `(33, 1280, 720)`, and the example crop had shape `(33, 576, 360)`. However, the notebook did not verify mouth localization, crop quality, labels, model readiness, or training suitability. Its resolution warning is caused by checking for `1280x720` while the actual consistent orientation is `720x1280`. Please explain why a sanity-check notebook was generated instead of a complete lip-reading training pipeline, and what should be built next for this dataset.