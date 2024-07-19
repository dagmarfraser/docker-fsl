# docker-fsl

- A container for FSL

  use the Dockerfile with this command
```
docker build -t fsl-image .
```
We probably want  3.0.6.5 in future so we cna have multiple versions

## How to use this container

- cd to the working directory in which you have neuroimaging data

- Pull the image and **mount working directory as /home/brain in the container**

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


To exit from this running container, you can use ctrl+c, ctrl+d or enter exit in the terminal.


