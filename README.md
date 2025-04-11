# Vid
Create Video Editing Pipelines With AI

```ts
// THIS IS A DREAMED LIBRARY
// I HAVE WRITTEN THE BASE AND PROMPTED AI TO EXTEND IT
// USING AI's POWER TO MAKE SENSE OUT OF ABSTRACT CONCEPTS

export const Effect = {
    Saturate: async (v: any) => { },
    Blur: async (v: any, f: any) => { },
    Transform2D: async (v: any, m: any) => { },
    Crop: async (v: any, r: any) => { }
};

export const Sequencer = {
    join: async (v: any[]) => { },
    split: async (v: any, splits: any) => { },
    frames: async (v: any) => { }
}

export const Composer = {
    Add: async (v1: any, v2: any) => { },
    Overlay: async (v1: any, v2: any) => { }
}

export const Input = {
    Script: async () => { },
    Audio: async () => { },
    Video: async () => { },
    Image: async () => { },
    Text: async () => { },
    Float: async () => { },
    Vec2: async () => { },
    Vec3: async () => { },
    Vec4: async () => { },
    Polygon: async () => { },
    TransformMatrix2D: async () => { },
}
export const Output = {
    Audio: async (v: any) => { },
    Image: async (v: any) => { },
    Video: async (v: any) => { },
    Text: async (v: any) => { }
}

export const Detect = {
    Context: async (v: any) => { },
}

export const AI = {
    RemoveNoise: async (v: any) => { },
}


//examples for AI
//we will be writing async functions that create and edit a video
//we can call these vedit, short for edited videos
//example 0: 
const create = async () => {
    //waits for user to upload a video
    const v = await Input.Video();
    //applies effects
    //Saturates the video using automatic setting
    const sv = await Effect.Saturate(v);
    //Blurs the value using the user supervised value
    const blurred = await Effect.Blur(sv, await Input.Float());
    //applies transform2D
    const final = await Effect.Transform2D(blurred, await Input.TransformMatrix2D());
    return final;
}
//example 1: 
const createMeme = async () => {
    //waits for user to upload a video
    const v = await Input.Video();
    const p = await Input.Image();
    //composiiton
    const transformedPhoto = await Effect.Transform2D(p, await Input.TransformMatrix2D());
    const overlayed = await Composer.Overlay(v, transformedPhoto);
    return overlayed;
}
//example 2: 
//using a vedit inside another vedit
const createMeme2 = async () => {
    //waits for user to upload a video
    const v = await Input.Video();
    const meme = await createMeme();
    //sequences the input video with the meme video
    const final = await Sequencer.join(v, meme);
    return final;
}

//example 3:
//creating a rather difficult vedit
//this vedit makes a talking head video
const createTalkingHead = async () => {
    //waits for user to upload a video
    const v = await Input.Video();
    //applies transform2D
    const tv = await Effect.Transform2D(v, await Input.TransformMatrix2D());
    const cropped = await Effect.Crop(tv, await Input.Vec4());
    const audio = await Output.Audio(v);//extracts the audio from the video
    const contexts = await Detect.Context(audio);
    const clips = await Sequencer.split(cropped, contexts);
    const userScript = await Input.Script();
    const shortened = clips.filter((c: any, i: number) => await userScript(c, i));
    const joined = await Sequencer.join(...shortened);
    const final = await AI.RemoveNoise(joined, await Input.Float());
    return final;
}

//add automatic labels to inputs
const createTalkingHead = async () => {
    // Step 1: Ask the user to upload a video
    const v = await Input.Video("Upload a talking head video");

    // Step 2: Ask for 2D transform matrix to apply to the video
    const transformMatrix = await Input.TransformMatrix2D("Provide a 2D transform matrix (scale, rotate, translate)");
    const tv = await Effect.Transform2D(v, transformMatrix);

    // Step 3: Ask for crop rectangle (x, y, width, height)
    const cropVec = await Input.Vec4("Enter cropping rectangle as [x, y, width, height]");
    const cropped = await Effect.Crop(tv, cropVec);

    // Step 4: Extract audio from the original video
    const audio = await Output.Audio(v);

    // Step 5: Detect context (e.g., speech segments or silent gaps)
    const contexts = await Detect.Context(audio);

    // Step 6: Split the cropped video based on detected audio contexts
    const clips = await Sequencer.split(cropped, contexts);

    // Step 7: Ask user for a custom script that decides which clips to keep
    const userScript = await Input.Script("Provide a filtering function to keep or discard clips");

    // Step 8: Apply the user script to filter clips
    const shortened = clips.filter((c: any, i: number) => await userScript(c, i));

    // Step 9: Join the remaining clips into a single video
    const joined = await Sequencer.join(...shortened);

    // Step 10: Ask user how aggressively to apply noise removal
    const noiseLevel = await Input.Float("Set noise removal level (e.g., 0.0 to 1.0)");
    const final = await AI.RemoveNoise(joined, noiseLevel);

    // Done!
    return final;
}

//Example 4:
// Using a Canvas to Add Effects on Manually Trimmed Array of Videos

const createMasterPiece = async () => {
    // Step 1: Ask user to give a bunch of videos
    const videos = [];
    const count = 0;
    while (true) {
        try {
            const v = await Input.Video(`Upload video ${count}`);
            const start = await Input.Float(`Set start`);
            const end = await Input.Float(`Set end`);
            const trimmed = await Sequencer.split(v, [start, end])[1];
            videos.push(trimmed);
        }
        catch (e) {
            break;
        }
    }

    // Step 2: Create a canvas
    const canvas = new HTMLCanvasElement();
    canvas.width = 1920;
    canvas.height = 1080;

    // Step 3: Create a context
    const context = canvas.getContext("2d")!;

    // Step 4: Process Each Image on Canvas
    const outFrames = [];
    for (const video of videos) {
        const frames = await Sequencer.frames(video);
        frames.forEach((frame) => {
            context.drawImage(frame, 0, 0, canvas.width, canvas.height);
            outFrames.push(context?.getImageData());
        })
    }
    return await Sequencer.join(...outFrames);
}


// EXAMPLES CREATED BY CHATGPT

const addWatermark = async () => {
    // Step 1: Get the main video from user
    const video = await Input.Video("Upload the video to watermark");

    // Step 2: Get the watermark image from user
    const watermark = await Input.Image("Upload watermark image");

    // Step 3: Get user-defined scale and position for the watermark
    const transform = await Input.TransformMatrix2D("Enter transform to scale and position the watermark");

    // Step 4: Transform the watermark
    const transformedWatermark = await Effect.Transform2D(watermark, transform);

    // Step 5: Overlay watermark on the video
    const finalVideo = await Composer.Overlay(video, transformedWatermark);

    return finalVideo;
}

const addSubtitles = async () => {
    // Step 1: Get the base video
    const video = await Input.Video("Upload your video");

    // Step 2: Get subtitle text as overlay
    const subtitleText = await Input.Text("Enter subtitle text");

    // Step 3: Transform text overlay into a visual format (e.g., static image)
    const textImage = await Effect.Transform2D(subtitleText, await Input.TransformMatrix2D("Transform subtitle placement"));

    // Step 4: Overlay the subtitle text image onto the video
    const finalVideo = await Composer.Overlay(video, textImage);

    return finalVideo;
}

const createPIP = async () => {
    // Step 1: Get main background video
    const backgroundVideo = await Input.Video("Upload the background/main video");

    // Step 2: Get small overlay video (e.g., webcam feed)
    const overlayVideo = await Input.Video("Upload the picture-in-picture video");

    // Step 3: Get transform to resize/move the overlay
    const overlayTransform = await Input.TransformMatrix2D("Position and scale the overlay video");

    // Step 4: Apply transform to the overlay video
    const smallOverlay = await Effect.Transform2D(overlayVideo, overlayTransform);

    // Step 5: Overlay small video on the main one
    const result = await Composer.Overlay(backgroundVideo, smallOverlay);

    return result;
}

const createRewindWithEcho = async () => {
    // Step 1: Upload video
    const original = await Input.Video("Upload a video to reverse");

    // Step 2: Extract audio and reverse the video
    const audio = await Output.Audio(original);
    const reversed = (await Sequencer.split(original, [0]))?.reverse(); // simulate reversing

    // Step 3: Add echo-like delay by sequencing video twice
    const echoClip = await Sequencer.join(...reversed, ...reversed);

    // Step 4: Overlay the audio again (could also process audio separately)
    return echoClip;
}

const maskVideoWithPolygon = async () => {
    // Step 1: Get the video
    const video = await Input.Video("Upload a video");

    // Step 2: Get polygon mask shape
    const polygon = await Input.Polygon("Draw a polygon mask");

    // Step 3: Generate a mask image with the polygon (you would need helper)
    const mask = await Effect.Transform2D(polygon, await Input.TransformMatrix2D("Mask positioning"));

    // Step 4: Overlay mask on the video
    const maskedVideo = await Composer.Overlay(video, mask);

    return maskedVideo;
}

//PROMPT:
// take multiple input of videos/images
// for each image set blur manually
// and give transform controls to make a collage
// and composite them
// pass images through a custom  input script
// for processing
const createCollageWithScript = async () => {
    const items: any[] = [];

    // Step 1: Input loop — collect multiple images or videos
    let count = 0;
    while (true) {
        try {
            const type = await Input.Text(`Add media #${count + 1}: Enter 'image' or 'video' (or leave blank to finish)`);
            if (!type) break;

            const media = type === "image" ? await Input.Image(`Upload image #${count + 1}`) : await Input.Video(`Upload video #${count + 1}`);

            // Step 2: Set blur per item
            const blurAmount = await Input.Float(`Set blur level for item #${count + 1} (e.g. 0 to 5)`);
            const blurred = await Effect.Blur(media, blurAmount);

            // Step 3: Transform for collage positioning
            const transform = await Input.TransformMatrix2D(`Set transform (scale, rotation, position) for item #${count + 1}`);
            const positioned = await Effect.Transform2D(blurred, transform);

            items.push(positioned);
            count++;
        } catch (err) {
            console.warn("Input interrupted", err);
            break;
        }
    }

    if (items.length === 0) {
        throw new Error("No media items were added.");
    }

    // Step 4: Apply a custom input script on the list of transformed media
    const processorScript = await Input.Script("Provide a processing function for each media item (v, index) => newV");

    const processedItems = [];
    for (let i = 0; i < items.length; i++) {
        const processed = await processorScript(items[i], i);
        processedItems.push(processed);
    }

    // Step 5: Composite all processed items into one final video
    let final = processedItems[0];
    for (let i = 1; i < processedItems.length; i++) {
        final = await Composer.Overlay(final, processedItems[i]);
    }

    return final;
};

```
