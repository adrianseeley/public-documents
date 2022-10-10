## Small Objects Workflow

**This is a guide for taking your captured photos through the photogrammetry modelling process in Reality Capture, and exporting them for use in Unity as LOD models.**

### Overview

1. Capture your photos in RAW format.
2. Delight, correct white balance, correct color, and crop photos using lightroom. Remove any blurred photos.
3. Export photos from lightroom into JPEG format.
4. Create a new Reality Capture project and add the JPEG folder.
5. Perform a draft alignment to generate a point cloud.
6. Measure your object and create a measured constraint on the point cloud.
7. Confirm images for constaint points.
8. Perform a full alignment to get a computed distance.
9. Create high detail model.
10. Remove artifacts.
11. Close holes.
12. Clean model.
13. Create a high detail texture on the cleaned model.
14. Simplify model to determine LOD0 triangle count and texture size.
15. Repeat for LOD1 and LOD2 (and LOD3 if required).
16. Export FBX files for Unity.
17. Import into Unity.
18. Create an LOD Group with physics.
19. Create a prefab for the object.

### 1. Capture your photos in RAW format.

*This guide won't be covering how to shoot your photos properly, theres a lot of information out there already for this.*

+ Although you can capture photos in a less detailed image format such as JPEG, capturing in RAW includes extra color information that can be used to correct white balance and color in post - this ensures that every object you capture is consistently neutral so that the lighting you apply in game looks realistic.
+ The photos for this example workflow were taken on a Nikon D7000 DSLR.
+ Always shoot at the lowest possible ISO sensitivity, these photos were shot at 100 ISO to reduce as much image sensor noise as possible, less noise means cleaner models.
+ Always shoot at a suitably narrow (read: high) aperture setting such as F22 or F26, we don't want any details on the object to be blurred due to shallow depth of field, more depth of field means cleaner models.
+ Specifically for small objects, using a longer focal length lens > 80 MM is reccomended to fill more of the frame with actual object and capture as much detail as possible.
+ Small objects should be shot in the studio, this example was shot inside an insanely well lit light box. Since the ISO is low and the aperture is narrow, we need way more light than a normal light box.
+ This pinecone was shot on a white 3D printed turntable, mounted directly on a stepper motor. The white turntable on a white background leads Reality Capture to ignore everything except the object, acting as though we circled the object. This saves a lot of headaches with masking and merging components.
+ 200 photos were taken, then the pinecone was flipped over and 200 more were taken for a total of 400 photos. 

### 2. Delight, correct white balance, correct color, and crop photos using lightroom. Remove any blurred photos.

+ Reality Capture will happily work with RAW images formats (Nikon RAW NEF in this case), but Adobe Lightroom is by far the superior workflow for preprocessing photos. A monthly license for Lightroom is under $30 and well worth it.
+ All photos should share the same develop settings since they share an identical controlled lighting setup.
+ Photos should be `delighted` or rather have their lighting neutralized, the idea is to remove any shadows and highlights, and bring them to neutral even lighting all over the object. Shooting in a lightbox provides almost perfect lighting conditions, meaning only small adjustments to the shadows / highlights sliders are required. If delighting isn't done objects may have weird shadows or highlights in the game that don't align with the in game lighting which is visually jarring.
+ Photos should have their `white balance` corrected using a true white card. If some objects are warm and other objects are cool it looks very disconnected in game and is visually jarring, thats why we always match the white balance to a single true white card.
+ Photos should have their `color corrected` using a color checker. This step is to get the truest representation of color possible from the start, every camera and image sensor is a bit different in how they capture color and the color checker acts as a reference value for how to transform the color to be closer to true color.
+ Photos should share an identical crop that is large enough to contain all rotations of the object, this removes as much background detail as possible, which only acts as noise in our case when kept in. By cropping like this Reality Capture has an easier time isolating the object.
+ Any photos that are blurred or imperfect should be removed, better to have one less photo than a blurred one. Always shoot way more than you need so you can remove bad photos without worry.

### 3. Export photos from lightroom into JPEG format.

+ We shoot in raw to maintain as much color information as possible, allowing us to handle preprocessing in Lightroom. Once that preprocessing is finished we can discard the extra color information and export the photos into 100% quality JPEGs.
+ Note that all RAWs and JPEGs should be saved and backed up on physical drives or a cloud service such as AWS S3. I personally use `S3 Sync` which is a great application for making sure everything is uploaded and up to date. AWS S3 pricing is actually pretty fair for this use case.
+ This pinecone has 8GB of photo data, don't try to store that in source control.

### 4. Create a new Reality Capture project and add the JPEG folder.

+ Open Reality Capture and create a new project, click on `add folder` to bring in your photos.

![](img/Screenshot%202022-10-08%20152155.png)

+ Depending on how you organized and exported your photos you should have a `jpeg` folder containing your already preprocessed photos.
+ Add this `jpeg` folder.

![](img/Screenshot%202022-10-08%20152218.png)

### 5. Perform a draft alignment to generate a point cloud.

+ Now that we have the photos in our project we will do a draft alignment. 
+ We will only use this alignment to provide measured dimensions to the object. 
+ Click on `Alignment > Draft`.

![](img/Screenshot%202022-10-08%20152328.png)

+ Once the alignment is completed we should see a single result, if you have more than one result your photos weren't shot properly or your object might have moved or deformed (leaves and foliage are bad for this). 
+ If you have multiple results, stop right here and go back and reshoot, if you start with garbage you can't expect to end with anything but garbage.
+ If you have a rolling object try hiding the tiniest dot of hot glue under the object to hold it in place while it rotates.
+ Foliage and leaves are tough, I've heard glue dipping or spraying clear adhesive on them can help firm them up, but expect to reshoot a few times to get a lucky round.
+ Rename this alignment result `draft alignment` using the selected component pane in the `1D view`. 
+ We will have a lot of objects by the end of this so naming things is absolutely key - especially in the future if you need to come back and re-export a model (spoiler: you will).

![](img/Screenshot%202022-10-08%20152532.png)

### 6. Measure your object and create a measured constraint on the point cloud.

+ Basically we mark two points on our object, then we measure that actual distance on the real object using calipers or a ruler. With this information Reality Capture will scale the object perfectly for us. Once we bring the object into Unity you will see that it is correctly sized.
+ Now that we have our draft point cloud we can use `Scene 3D > Tools > Define Distance`. 


![](img/Screenshot%202022-10-08%20152556.png)

+ Although it's difficult to see in this screenshot a blue bullseye cursor will appear over the point cloud (marked here by a red dot for clarity). We use this to place a distance marker.
+ I will be measuring the pinecone from end-to-end for my defined distance, so I place my first marker here at the fat end.

![](img/Screenshot%202022-10-08%20152631.png)

+ Once the first marker is placed a line will appear and the blue bullseye cursor will be active again.
+ Next we place the second marker.
+ I place my second marker here at tip of the skinny end.

![](img/Screenshot%202022-10-08%20152647.png)

+ Once both markers are placed our `1D view` will get populated by some new items, and a `selected constraints` panel will appear. 
+ In this panel we will provide a name to the new distant constraint that we have just created.
+ Here I have used the name `distance end to end`, but depending on what part of your object you measure there is likely a better name. 
+ Note also that the unit of measure here is the `Meter`, and to make this entire workflow as simple as possible I _strongly_ urge you to keep it meters. 
+ In the `defined distance` box place the real world measurement between the points you chose.
+ I measured this pinecone with a small digital caliper ($20 on amazon), and it measured `44mm` from end to end, which is `0.044m`. 
+ We can see in the `calculated distance` box Reality Capture is asking for more points to be assigned or more images. It needs us to confirm the two markers we used in a few photos.

![](img/Screenshot%202022-10-08%20152747.png)

### 7. Confirm images for constaint points.

* In the `1D view` under `point 0` we should have a list of every photo that Reality Capture thinks contains that point. 
* Go through these images one by one and view them in the `2D view` (shown here on the far right), the points should all be pretty accurate but every now and then one might need a slight adjustment. 
* To confirm the point  is in the correct location click on the `small green +` box next to the image name in the `1D view`. 
* Repeat this confirmation process for 5-10 images, for both `point 0`, and `point 1`.

![](img/Screenshot%202022-10-08%20152807.png)

+ Once completed you can collapse both `point 0` and `point 1`, and you should now see to the right of `point 0` and `point 1` is a list of how many images you have confirmed (`6`, and `2` here). 
+ We can also see the red caution markers under constraints and control points, thats normal.

![](img/Screenshot%202022-10-08%20152859.png)

### 8. Perform a full alignment to get a computed distance.

+ Now that Reality Capture is aware of the true scale of the object we need to perform a new alignment to set this scale properly.
+ This time we will use `Alignment > Align Images` to do a full alignment and not just a draft. The draft alignment was only used to add a measured dimension in the simplest way.

![](img/Screenshot%202022-10-08%20152921.png)

+ Once completed We will have a new component in the `1D view`, and if you select the `distance end to end` constraint you can see that the `calculated distance` is now properly populated by Reality Capture, and on our point cloud you can see that the measured dimension shown here matches that value.

![](img/Screenshot%202022-10-08%20153231.png)

* We select the new component and name it `final alignment`.

![](img/Screenshot%202022-10-08%20153247.png)

### 9. Create high detail model.

+ Now is a good time to collapse everything in the `1D view` except for the item we are working on (the `final alignment`).
+ You can also close out any panels below the `1D view` from any still open tools or inspectors. 
+ Next we will generate the model in the highest detail possible by selecting `Mesh Model > High Detail`.

![](img/Screenshot%202022-10-08%20153421.png)

+ Once completed, we should have a single result. If you get more than one result go back and reshoot. You can try to merge components but it's always better to reshoot.
+ Next we rename this model to `raw`. 
+ This `raw` model has the maximum amount of detail, but it also has some minor issues we need to fix up before we texture it.

![](img/Screenshot%202022-10-08%20154235.png)

### 10. Remove artifacts.

+ Artifacts are small pieces of mesh floating around like islands, or even attached to the mesh itself, we want to remove these as they aren't part of the actual model.
+ It's rare to have zero artifacts so be very careful to rotate all around your model and look for artifacts that might be hiding. 
+ We select `Scene 3D > Tools > Lasso`, which will let us draw circles around any artifacts to select them for removal.

![](img/Screenshot%202022-10-08%20154250.png)

* Rotate your object around and use `ctrl + click` to add more artifacts to the active selection. 
* We want to select all the artifacts in a single selection and filter them out all at once to prevent creating extra models for a single step. 
* You can see here that the artifacts actively selected are now highlighted in orange. 
* Once we're sure we have all the artifacts selected, we will use `Scene 3D > Filter Selection` to remove them.
* `Filtering` in this case means to remove, literally we are removing those selected artifacts (read: filtering them out) from the model and making a new version of the model without them.

![](img/Screenshot%202022-10-08%20162606.png)

+ Now that we have the artifacts removed, we can move onto cleaning up issues that are usually invisible.
+ Before we move on you will notice that a new model has appeared below our `raw` model in the `1D view` and it may or may not have a smaller number of triangles based on how many artifacts were filtered out (here we filtered out 0.1M tris). 
+ Next we rename this new model `artifacts removed`.
+ The idea behind naming our steps like this is that anyone who opens this project in the future can see the exact steps taken, in the exact order, and the results of those steps.
+ Photogrammetry and `LOD assets` (level of detail) is a back and forth process between Unity and Reality Capture. You will frequently bring an asset into the game and realize that a much different level of detail is required than you originally thought causing you to come back and make changes later.

![](img/Screenshot%202022-10-08%20162645.png)

### 11. Close holes.

+ Before continuing, this is a good time to note the small blue eye icon next to the actively displayed model name. 
+ In this image it is next to `artifacts removed`, and we can see that this is also displayed in the `selected models` pane below it. 
+ **Always** make sure you have the right model selected before you grab a tool to work with! 
+ Meshes almost always end up with tiny holes somewhere in them, and we seal them up using `Scene 3D > Tools > Close Holes`.

![](img/Screenshot%202022-10-08%20162703.png)

+ Once the `close holes` tool is selected a pane will appear under the `1D view` called `close holes tool`.
+ I haven't run into a situation where I've needed to change the `max edge count` yet but your mileage may vary.
+ Confirm you have the correct model selected and click on `close holes` in this pane.

![](img/Screenshot%202022-10-08%20162716.png)

### 12. Clean model.

* Note that the `close holes` tool does not make a new model, it modifies the existing one, our `artifacts removed` model in this case. 
* If you want to be overly pedantic you can duplicate the `artifacts removed` model, perform `close holes` on the duplicated model and provide it with a new name like `holes closed`. We don't do that here in this case.
* Next we select `Scene 3D > Tools > Clean Model` which will correct any other small model issues that we might not see.

![](img/Screenshot%202022-10-08%20162813.png)

* Once completed we rename the new model to `cleaned model`.
* This is now our master mesh, containing the maximum amount of detail with all cleaning procedures having already been performed.

![](img/Screenshot%202022-10-08%20162901.png)

### 13. Create a high detail texture on the cleaned model.

+ Next we need to generate the master texture by selecting `Mesh Model > Texture`, making sure that the cleaned model (our master) is the active one.

![](img/Screenshot%202022-10-08%20162944.png)

+ Once completed we can see we now have model textures, in this case `1x4k` color texture. Depending on the size and detail of your object you may have more or less textures.
+ Note that there are no normal textures yet since all that information is currently stored in the triangles themselves. As we make simplified versions of the model we trade extra triangles for normal textures.

![](img/Screenshot%202022-10-08%20163217.png)

### 14. Simplify model to determine LOD0 triangle count and texture size.

+ Our master model is insanely large by game engine standards, we will never export this model for use in game, instead we will simplify it into a much lighter model.
+ `LOD0` is the most detailed version of our object that will make it to the game, and we want it to be as detailed as possible.
+ The goal in this step is to remove as many triangle, and as much texture space as possible without the appearance of the object changing at all. Literally we want the object to look identical to the master but weigh less in terms of triangles and textures.
+ We do this by using the simplify tool: `Scene 3D > Tools > Simplify Tool`.
+ Note we always simplify from the master, and not another already simplified model. In this way we get the most accurate reprojection possible.
+ `Reprojection` is the process Reality Capture uses to map textures and normals to a model with less triangles than the original, literally projecting them onto the simplified model.

![](img/Screenshot%202022-10-08%20163450.png)

+ We can choose either `relative` or `absolute` simplification, `relative` allows us to specify a percentage of the original triangle count as a target, and `absolute` lets us specify the exact number of triangles. 
+ I personally prefer using `absolute` because I can better estimate how many triangles are close to the target we are after.
+ Ensure that both `color reprojection` and `normal reprojection` are set to `enabled`, without this Reality Capture won't create new color and normal textures for us.
+ Again the goal here is to find the magical combination of triangle count and texture size that is as small as possible while visually looking no different.
+ Maximum texture sizes can be specified under `unwrap paramters`, I have left it as default in this case to carry through the `1x4k` texture size.

![](img/Screenshot%202022-10-08%20163514.png)

+ It is tedious and time consuming, but I reccomend trying 5-15 different combinations of simplify settings to really dial in the `LOD0` model as optimally as possible.
+ Once you land on the magical combination where any further reduction results in visual changes, we delete any other models we generated, and rename the winner `lod0`.
+ In this case I ended up on 500 triangles with `1x4k` texture, a major step down from over 2M triangles and visually no different even when zoomed in to take up the entire screen.

![](img/Screenshot%202022-10-08%20163841.png)

### 15. Repeat for LOD1 and LOD2 (and LOD3 if required).

+ For almost any small object three levels of detail `LOD0`, `LOD1`, and `LOD2` - are more than enough, but some special objects may require an extra level of detail `LOD3` or potentially more. This pinecone doesn't need the fourth level of detail but I will create one anyways to illustrate the workflow changes when it reaches Unity.
+ My rule of thumb is that `LOD0` is used whenever the object is shown bigger than a toonie (Canadian two dollar coin) held up to the screen. `LOD1` is used whenever the object is shown smaller than a toonie, but larger than a Canadian dime, and `LOD2` is used whenever the object is shown smaller than a Canadian dime.
+ This is a good starting point, but every object is different, especially if you need more than three levels of detail, don't be afraid to break away from the guideline to get the best result possible.
+ Next we repeat the process of simplfying the master that we used to create the `lod0` model, but this time we zoom out the `3D view` to the target LOD display size (ie. a toonie or a dime) and comparing the newly simplified model to the master at this zoom level.
+ Here I was able to drop all the way down to 100 triangles, and `1x2k` texture.

![](img/Screenshot%202022-10-08%20164000.png)

+ Once we find our `lod1` we rename the model to `lod1` and delete any other trial models that we created while searching. 
+ As we can see here `lod1` at this zoom level this looks pretty good.

![](img/Screenshot%202022-10-08%20164207.png)

+ We repeat this process to create `lod2`, checking quality by zooming out until the display of the object is smaller than a Canadian dime held up to the screen.

![](img/Screenshot%202022-10-08%20164403.png)

+ Some objects might simplify down in a weird way and require an extra level of detail `LOD3`, this one doesn't but I'll make an extra level of detail just to show the process in Unity later on.

![](img/Screenshot%202022-10-08%20164555.png)

+ I landed on 16 triangles, and a `1x512` texture for `lod3`, and when viewed zoomed way out it looks just fine.

![](img/Screenshot%202022-10-08%20164609.png)

+ I've expanded the `1D view` here to show the final result of generating 4 levels of detail.
+ The naming scheme we used effectively documents all the steps we took on the way to generating our LOD models. 
+ You can see how each further level of detail has less triangles and smaller textures. 
+ Don't look at this and think "this is how every object should look", that's the wrong idea, you have to treat each object as the unique thing that it is and optimize it accordingly.

![](img/Screenshot%202022-10-08%20164722.png)

### 16. Export FBX files for Unity.

+ Now that we have everything ready to go we save the Reality Capture project in a subfolder called `project`. 
+ You can see in the directory that we have the path `SmallObjects/PineCone_00`, then in that folder all of the raw photos straight out of camera, the jpegs exported from Lightroom, and now a Reality Capture project folder.

![](img/Screenshot%202022-10-08%20164743.png)

+ As part of an organizational strategy we name every Reality Capture project, `project`. 
+ If you follow a strict naming scheme like this it's much easier to write command line tools and scripts to act on one or more Reality Capture projects - depending on your game project this will likely need to happen at some point.

![](img/Screenshot%202022-10-08%20164806.png)

+ Now that our project is saved it's finally time to do some exporting! 
+ We don't export any of the models we created on our way to the LOD models since these won't ever be used in the game. 
+ If we ever need access to these intermediary models again, they are forever saved in this project waiting for us to come back. 
+ Next we select the `lod0` model, and confirm it is the selected model.

![](img/Screenshot%202022-10-08%20164952.png)

+ Then we open the main menu in the top left corner and select export.

![](img/Screenshot%202022-10-08%20165123.png)

+ For exporting into a format Unity can work well with we use the FBX format, it contains more information about the model than the OBJ format does.
+ The OBJ format is really only good for moving models between programs that don't support the FBX format.

![](img/Screenshot%202022-10-08%20165140.png)

+ Once we select our export type Reality Capture will request a folder to place the output.
+ We create a new folder in our object's directory called `fbx`.
+ Within the `fbx` fodler we then create a new folder called `lod0`.
+ Every exported model is named `project`, which is intended to make command line tools and scripting easier to work with later.
+ This will eventually result in a folder that looks something like this.

![](img/Screenshot%202022-10-08%20165441.png)

+ Once an export location is chosen Reality Capture will provide a lot of export options, I'll try to cover the important ones.
+ `export an info file: yes` this provides an `.rcinfo` file which can be used by Reality Capture in the future to bring the fbx file back in. I haven't had to do this yet but it certainly doesn't hurt to have it.
+ `save mesh by parts: no` since our model is a single part, and not a massive object like a whole church we have no need to break things into parts.
+ `export vertex normals: yes` this bulks up our vertex size a little bit but if you are getting into some technical shaders or realtime effects there's a good chance you might need to use these vertex normals for something. I always export them, and if the GPU budget falls short during developement these normals can be easily removed later on if they aren't needed for some free gains.
+ `export vertex colors: yes` same as the normals, there are some interesting uses for this color information on vertices when working with shaders later on so I keep it in, until we absolutely have to optimize it out.
+ `export textures: yes` unless you're doing a 3D print of your mesh you want the color/normal textures.
+ `color layer > export layer: yes` we want the color texture(s).
+ `normal layer > export layer: yes` we want the normal texture(s), without these our models will look much worse, in some rare cases an object won't need a normal texture, but again better to export it and just not use it.
+ `transformation preset: unity` this one is pretty key, it will give us a format and coordinate system that Unity will understand without us needing to do any other work.
+ Every other setting is left as the default. 
+ Note that because we did a physical measurement of our object and added a distance constraint, the object's size will be life-size accurate at `scale = 1.0` when we bring it into Unity.

![](img/Screenshot%202022-10-08%20165403.png)

+ After completing the export of `lod0` we repeat the process with each of the `lod*` models until we end up with an `fbx` folder looking like the following image. 
+ At this point all 4 levels of detail have been exported and are ready to be brought into Unity. 
+ This is usually when I would backup and sync all these assets to Amazon S3 using S3 Sync. This pinecone is about 8 GB worth of data, so source control isn't really the best place for this specifically. The `lod*` models themselves will be copied into the Unity directory project, and _those alone_ will be checked into source control, in my case GIT LFS on Github which has surprisingly great pricing ($5/50gb/month).

![](img/Screenshot%202022-10-08%20165545.png)

+ If we look at the sizes of each `lod*` folder you can see they are the right size for storing in source control.
+ If you use GIT LFS make sure you track `*.fbx` and `*.png` files for large file storage.

![](img/Screenshot%202022-10-08%20165621.png)

![](img/Screenshot%202022-10-08%20165636.png)

![](img/Screenshot%202022-10-08%20165646.png)

![](img/Screenshot%202022-10-08%20165658.png)

### 17. Import into Unity.

* Finally time to open up Unity, and either create a new project or open an existing one.
* We will setup a similarly named folder structure inside the Unity project where we will copy in the pinecone `lod*` folders by dragging and dropping.

![](img/Screenshot%202022-10-08%20170030.png)

* During importing Unity will complain that the normal textures are by default being imported as color textures, and it will ask if you want them to be changed to normal textures instead, which we do by pressing `fix now`.

![](img/Screenshot%202022-10-08%20170105.png)

+ Once that's completed you can see we have a nice manageable directory structure `Models/SmallObjects/PineCone_00/lod*/project.fbx`.
+ Any sizeable project could have hundreds if not thousands of objects, so organize early and organize often, and don't be afraid to go back and reorganize everything once in a while!

![](img/Screenshot%202022-10-08%20170130.png)

### 18. Create an LOD Group with physics.

+ Next we need to setup Unity to use this as an LOD object, we start by creating a new empty object in the scene.

![](img/Screenshot%202022-10-08%20170143.png)

+ We provide a name that matches the directory structure and object name: `SmallObjects/PineCone_00`.
+ This keeps everything in your scene and project structure clean, obvious, and easy to work with.
+ During debugging this will provide a quickly readable display of exactly what types of objects and what objects specifically are making up a scene.

![](img/Screenshot%202022-10-08%20170239.png)

+ Next on our new game object we add an `LOD Group` component. 
+ This is a Unity component that will take all of our of `lod*` models and automatically swap between them based on how much screen space the object takes up when being displayed.

![](img/Screenshot%202022-10-08%20181434.png)

+ Unity expects the `lod*` models to be child objects, so one by one we drag each model in as children to the `SmallObjects/PineCone_00` object, and provide names `lod0` through `lod3` in this case.

![](img/Screenshot%202022-10-08%20181550.png)

+ We make sure to name each level of detail as we add it otherwise we might mix up levels of detail.

![](img/Screenshot%202022-10-08%20181614.png)

+ Once completed we are left with all of our levels of detail as children, as shown below.
+ Note that most objects will only have three levels of detail, but I have added four here just to showcase some extra workflow for those situations in Unity.

![](img/Screenshot%202022-10-08%20181650.png)

+ Next we select the parent object.
+ Then in the inspector pane for the `LOD Group` select `LOD 0`.
+ Then under `renderers` click on `add` to add our first `lod*` model.

![](img/Screenshot%202022-10-08%20181710.png)

* Since we already setup our child objects the way Unity expects, this list is nicely populated for us.
* Here select `lod0` for the `LOD 0` slot.

![](img/Screenshot%202022-10-08%20181722.png)

+ We repeat this process for each LOD level, but notice that Unity only has 3 LOD slots by default, then the final slot is `culled`. 
+ `culled` is used to stop drawing the object once it is being displayed under a certain size. Literally culling it from the view. 
+ For some objects where we need an extra LOD level, we can right click on any of the LOD levels and select `insert before`, then add our final LOD level in there.

![](img/Screenshot%202022-10-08%20181743.png)

+ Once completed we can now see the different object sizes in terms of triangles.

![](img/Screenshot%202022-10-08%20181856.png)

+ This is where each object gets a little different.
+ We can adjust the size of each LOD zone by sliding the edges left and right to change where the transition between each model happens. Literally at what size on screen should we switch to each level of detail.
+ We can also change the fade mode to adjust how Unity displays the transitions between the levels of detail.
+ I have opted for no fade mode here as the model transitions fairly smoothly on it's own.

![](img/Screenshot%202022-10-08%20182750.png)

+ When it comes to working with this object now, we treat the parent as the actual object and ignore the children which Unity will use to handle the rendering for us. 
+ We can go ahead and add a sphere collider here for example to provide some collision detection to our object.

![](img/Screenshot%202022-10-08%20182942.png)

+ When adjusting your collider make sure to use the little blue camera icon in the `LOD Group` component to swap between models to ensure that your collider lines up with all of them well enough. Normally this isn't an issue.

![](img/Screenshot%202022-10-08%20183207.png)

+ We can also add a rigidbody here to provide some physics to the object.

![](img/Screenshot%202022-10-08%20183242.png)

### 19. Create a prefab for the object.

+ Once we have our object setup the way we like it, we can drag the parent object into our model folder to create a fully setup LOD prefab in an easy to find place.

![](img/Screenshot%202022-10-08%20183839.png)

+ Now we can use this prefab in our scenes, and we have a fully functional, photogrammetric small object, with 4 levels of detail, inside Unity. 
+ As a reminder these models and prefabs will get checked into source control, but all the raw and jpeg photos, and Reality Capture project files do not due to their size - instead they are stored on AWS S3.

![](img/Screenshot%202022-10-08%20183901.png)

+ Once you get into this workflow you will find you need to go back and forth between Reality Capture and Unity a few times to really dial in your models and make optimizations. 
+ Often what happens later on in a project during an optimization pass, every model might be scrutizined for what it contributes to a scene versus the GPU budget required to display it at a certain visual fidelity. 
+ When this happens I won't remove the old `lod*` objects in Reality Capture, instead I will create new ones as a new version `lod*V2` (ie. `lod0V2`, `lod1V2`, etc). 
+ I also keep the original exports and prefabs in Unity and create v2 folders for the new exports.
+ Being able to instantly swap between the old and new prefabs at the click of a button is so critical for optimizing a scene and visually comparing options.

### Done!