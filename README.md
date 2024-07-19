# docker-fsl

- A container for FSL

  use the Dockerfile with this command
```
docker build -t fsl-image .
```
We probably want -t 3.0.6.5 in future so we cna have multiple versions

## How to use this container

- cd to the working directory in which you have neuroimaging data

- Pull the image and **mount working directory as /home/brain in the container**

```
docker run -it --rm -v $PWD:/home/brain docker-fsl:6.0.6.2
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
To exit from this running container, you can use ctrl+c, ctrl+d or enter exit in the terminal.


