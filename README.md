# FunctionObjects_OpenFOAM
List of useful OpenFOAM function objects

1. `Probes` function object : to monitor pressure and velocity at critical points across the domain.

```bash
probes
{
    type            probes;
    libs            ("libsampling.so");

    // Name of the directory for probe data
    name            probes;

    // Write at same frequency as fields
    writeControl    timeStep;
    writeInterval   1;

fields (
        p 
        U 
       );

probeLocations
(

	(0.03 0.006 0.006)
	(0.03 0.006 0)
	(0.03 0.006 -0.006)
	
);
    // Optional: filter out points that haven't been found. Default
    //           is to include them (with value -VGREAT)
    includeOutOfBounds  true;
}
```

2. `fieldAverage` function object : to calculate the time-averaged pressure and velocity fields, as well as their root-mean-squared covarience.

```bash
fieldAverage
    {
        type            fieldAverage;
        libs            ("libfieldFunctionObjects.so");
        writeControl    writeTime;
        timeStart       0;

        fields
        (
            U
            {
                mean        on;
                prime2Mean  on;
                base        time;
            }

            p
            {
                mean        on;
                prime2Mean  on;
                base        time;
            }
        );
    }
```

3. `forces` or `forceCoeffs` function object : to calculate the force and force coefficient on any projected surface.

```bash
forces1
    {
        type            forces;
        libs            (forces);
        writeControl   timeStep;
        writeInterval  1;

        patches         ( "PRISM" );
        CofR            (0 0 0);

        rho             rhoInf;
        log             true;
        rhoInf          1.225;
    }

forceCoefficients
    {
        type            forceCoeffs;
        libs ( "libforces.so" );
    
        writeControl   timeStep;
        writeInterval  1;
        
        patches         ( "PRISM" );
        
        rho             rhoInf;
        log             true;
        rhoInf          1.225;
        liftDir         (0 1 0);
        dragDir         (1 0 0);
        //sideDir         (0 0 1);
        CofR            (0 0 0);
        pitchAxis       (0 0 1);
        magUInf         0.015;
        lRef            0.1;
        Aref            0.0628;
    }
```

4. `vorticity` function object : to calculate the curl of velocity.

```bash
vorticity
    {
        // Mandatory entries
        type            vorticity;
        libs            (fieldFunctionObjects);

        // Optional (inherited) entries
        field           U;
        result          vorticityField;
        region          region0;
        enabled         true;
        log             true;
        executeControl  timeStep;
        executeInterval 1;
        writeControl    timeStep;
        writeInterval   42;
    }
```

5. `Lambda2` function object : used to identify vortex cores.

```bash
Lambda2
{
    // Mandatory entries
    type            Lambda2;
    libs            (fieldFunctionObjects);

    // Optional (inherited) entries
    field           U;
    result          Lambda2Field;
    region          region0;
    enabled         true;
    log             true;
    executeControl  timeStep;
    executeInterval 42;
    writeControl    timeStep;
    writeInterval   42;
}
```

6. `subtract` function object : to calculate the turbulence intensity, i.e. the fluctuating component of velocity.

```bash
subtract
{
    // Mandatory entries
    type            subtract;
    libs            (fieldFunctionObjects);
    fields          (U UMean);

    // Optional (inherited) entries
    result          uPrime;
    region          region0;
    enabled         true;
    log             true;
    executeControl  timeStep;
    executeInterval 1;
    writeControl    timeStep;
    writeInterval   42;
}
```

7. `surfaces` function object : extract field data at specified planes.

```bash
surfaces
{
    type            surfaces;
    libs            ("libsampling.so");
    writeControl    writeTime;

    surfaceFormat   vtk;
	
	formatOptions
	{
		vtk
		{
			legacy true;
			format ascii;
		} 
	}
	
    fields          (p U);

    interpolationScheme cellPoint;

    surfaces
    {
        zNormal
        {
            type        cuttingPlane;
            point       (0 0 0);
            normal      (0 0 1);
            interpolate true;
        }

              yNormal
        {
            type        cuttingPlane;
            point       (0 0.005 0);
            normal      (0 1 0);
            interpolate true;
        }
    };
}
```
