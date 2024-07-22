# docker-fsl

- A container for FSL

  use the Dockerfile with this command
```
docker build -t fsl-image .
```
We probably want  3.0.6.5 explicitly tagged in future so we can have multiple versions

## How to use this container

- cd to the working directory in which you have neuroimaging data

- Pull the image and also **mount working directory as /home/brain in the container**

```
docker run -it --rm -v $PWD:/home/brain fsl-image
```

- You can run FSL commands after cd to the home directory

```
cd # cd to /home/brain
```
- e.g.
```
echo $FSLDIR.
```
- This should display the name of the directory that you installed FSL in.

- Check that your path is correct by typing:
```
flirt -version.
```
- to run this without interaction?
```
docker run --rm -v $PWD:/home/brain -t fsl-image fslinfo /home/brain/MNI152_T1_1.25mm_brain.nii.gz
```
 - lets put it on docker hub
 ```
 docker tag fsl-image daralathas/fsl-image:1
 docker push daralathas/fsl-image:1
```
And then on BLUEBear make the apptainer .sif file... and test it

```
$ cd /rds/projects/f/fraserds-mpo-evaluation  
$ apptainer pull docker://daralathas/fsl-image:1
$ apptainer exec fsl-image_1.sif python --version
$ apptainer shell fsl-image_1.sif
Apptainer> python --version
```
lets have a shell within - this path needs to exist in the container!
```
$ apptainer shell --bind $PWD:/home/brain fsl-image_1.sif
Apptainer> fslinfo /workdir/MNI152_T1_1.25mm_brain.nii.gz
```

So far so good

Now we want to see the Apptainer'd FSL GUI in the remote machine.. so on the Mac that should be easy. Install XQuartz and use the -X option to pipe the X11 back to us.

```
% ssh -X fraserds@bluebear.bham.ac.uk
$ cd /rds/projects/f/fraserds-mpo-evaluation
```
We want to run an interactive job.. so lets play nice and not work on the head node.

Our project will need to be GPU active to allow GPUs to be used.(https://docs.bear.bham.ac.uk/bluebear/bbgpu/?h=gpu)

```
$ module load slurm-interactive
$ fisbatch_screen --nodes=1-1 --ntasks=2 --time=1:0:0

```

- Lets try some OpenGl, Some Nvidia ... some software rendiring?
```
$ apptainer run --bind /tmp/.X11-unix:/tmp/.X11-unix \
              --bind $PWD:/home/brain \
              --env="DISPLAY=$DISPLAY" \
              --env="FSLEYES_FORCE_OPENGL_VERSION=2.1" \
              fsl-image_1.sif fsleyes

$ apptainer run --bind /tmp/.X11-unix:/tmp/.X11-unix \
              --bind $PWD:/home/brain \
              --env="DISPLAY=$DISPLAY" \
              --env="LIBGL_ALWAYS_SOFTWARE=1" \
              fsl-image_1.sif fsleyes

$ apptainer exec --nv \
               --bind /tmp/.X11-unix:/tmp/.X11-unix \
               --bind $PWD:/home/brain \
               --env="DISPLAY=$DISPLAY" \
              fsl-image_1.sif fsleyes
```
We get splash screen... but then some errors.... so no joy... but we are close!

According to this page...

https://docs.bear.bham.ac.uk/bluebear/bbgpu/
we just add 
```
#SBATCH --qos=bbgpu
#SBATCH --account=_projectname_
#SBATCH --gres=_gpu_
```
So
```
$ module load slurm-interactive
$ fisbatch_screen --nodes=1-1 --ntasks=2 --time=1:0:0 --qos=bbgpu --account=fraserds-mpo-evaluation --gres=gpu:a100:1
```
Alas still looks like we are missing wxPython... so let's do a new Docker with X11 extra explicitly baked in!

To exit from this running container, you can use ctrl+c, ctrl+d or enter exit in the terminal.


