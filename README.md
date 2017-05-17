# DeepDreamVideoOpticalFlow

# Use Optical Flow to Adjust Deep Dream Video

-The flow option enables the optical flow mode. This allows the optical flow of each frame to be calculated by comparing the difference in the movement of all pixels between the current and previous frame. 
The hallucinatory patterns on the area where the optical flow was detected is merged with the current (not-yet-hallucinatory) frame based on the weighting provided by the user defined blending ratio 
(0 = no information, 1 = all information). The blending ratio allows some of the hallucinatory content of the previous frame to be inherited from the previous frame, The Deep Dream algorithm is then applied to this merged frame, 
instead of Deep Dream starting from scratch for each frame. 

1) The difference in optical flow is calculated between the previous and current frame (before the Deep Dream algorithm is applied). 
2) The hallucinatory patterns within the area of high optical flow in the previous frame are shifted in the direction of the optical flow.
3) This hallucinatory pattern from the previous frame is merged into the current (not-yet-hallucinatory) frame with a specfied blending ratio. 
 
The blending ratio on the optical flow area and the other areas (background) can be specified separately by using -bm and -bs options.
The range of the blending ratio is between 0 and 1. A blending ratio of 1 means that the current frame inherits 100% of the hallucinatory content from the previous frame, and then the deep dream algorithm is applied.
A blending ratio of 0 means the previous frame is dicarded, therefore the deep dream algorithm is applied from the scrach. 


# Dividing the Image

When using a GPU to process the deep dream algorithm, the maximum image size is capped by the video memory of your GPU.
In order to process large images such as 4K resolution, it is neccersary to divide the input image into smaller sub-images inorder to apply Deep Dream.
-d or --divide option can be used to enable this function.

`-d 0 : disable image division
`-d 1 : divide the image by MAX_WIDTH and MAX_HEIGHT of image specified by -mw and -mh options
`-d 2 : divide the image half when the width of the image is larger than MAX_WIDTH specified by -mw option

The divided image is processed by Deep Dream individually and then merged into one.
If using octavescale in the original deep dream algorithm, the image is processed in a smaller size in the begining.
In that case, the image division will not be applied to the earlier stage of the itterations (before enlarging the original size).

# Usage

# Original Options:

 -h, --help            show this help message and exit
 -i INPUT, --input INPUT
                        Input directory where extracted frames are stored
 -o OUTPUT, --output OUTPUT
                        Output directory where processed frames are to be
                        stored
 -it IMAGE_TYPE, --image_type IMAGE_TYPE
                        Specify whether jpg or png
  --gpu GPU             Switch for gpu computation.
  -t MODEL_PATH, --model_path MODEL_PATH
                        Model directory to use
  -m MODEL_NAME, --model_name MODEL_NAME
                        Caffe Model name to use
  -p PREVIEW, --preview PREVIEW
                        Preview image width. Default: 0
  -oct OCTAVES, --octaves OCTAVES
                        Octaves. Default: 4
  -octs OCTAVESCALE, --octavescale OCTAVESCALE
                        Octave Scale. Default: 1.4
  -itr ITERATIONS, --iterations ITERATIONS
                        Iterations. Default: 10
  -j JITTER, --jitter JITTER
                        Jitter. Default: 32
  -z ZOOM, --zoom ZOOM  Zoom in Amount. Default: 1
  -s STEPSIZE, --stepsize STEPSIZE
                        Step Size. Default: 1.5
  -b BLEND, --blend BLEND
                        Blend Amount. Default: "0.5" (constant), or "loop"
                        (0.5-1.0), or "random"
  -l LAYERS [LAYERS ...], --layers LAYERS [LAYERS ...]
                        Array of Layers to loop through. Default: [customloop]
                        - or choose ie [inception_4c/output] for that single
                        layer
  -v VERBOSE, --verbose VERBOSE
                        verbosity [0-3]

# Additional Options:

   -flow, --flow	Optical Flow is taken into accout
   -flowthresh, --flowthresh
			Threshold for detecting a flow
   -bm BLEND_FLOW, --blendflow BLEND_FLOW
			blend ratio for flowing part
   -bs BLEND_STATIC, --blendstatic BLEND_STATIC
			blend ratio for static part
   -g, --guide GUIDE_IMAGE
			A guide image
   -d [0-2] --divide [0-2]
			dividing image into sub images [0:disable 1:dividing to maxWidth, maxHeight 2:dividing half if width exceeds maxWidth]
   -mw MAX_WIDTH, --maxWidth MAX_WIDTH
			Maximum width to devide image
   -mh MAX_HEIGHT, --maxHeight MAX_HEIGHT
			Maximum height to devide image (only used for the divide mode 1)


# Requirements

- Python
- Caffe (and other deepdream dependencies)
- FFMPEG
- CV2 (if you use optical flow)



# Examples
 1-extract.bat
 
`python dreamer.py -e 1 --input RHI-20Sec.mov --output Input

 2-dream.bat

`python dreamer.py --input Input --output Output --octaves 3 --octavescale 1.8 --iterations 16 --jitter 32 --zoom 1 --stepsize 1.5 --flow 1 --flowthresh 6 --blendflow 0.9 --blendstatic 0.1 --layers inception_4d/pool --gpu 1 -d 2 -mw 1500

 3-create.bat

`python dreamer.py -c 1 --input Output --output Video.mp4
