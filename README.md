PlaineMorteLakehydro_thermodynamics
==============================================


The `notebooks` process the data acquired during the 2019 field campaign on Plaine Morte to obtain the results and the figures presented in: "Drainage of an ice-dammed lake through a supraglacial stream: hydraulics and thermodynamics" submitted in The Cryosphere by Ogier et al (May 2021).


**Computing environment and packages installations**

These scripts were developed in Julia v1.5.2 (although later versions should work too) using Jypyter notebooks via the IJulia package (https://github.com/JuliaLang/IJulia.jl).  The packages are recorded in their used version in the Manifest.toml file.

To run these scripts:
- install Julia 1.5, probably via a download from https://julialang.org/downloads/oldreleases/.
- start the Julia REPL (terminal) within the folder of this README-file with `julia --project`
- install the packages at the versions recorded within the Manifest.toml: `using Pkg; Pkg.instantiate()`
- make sure that IJulia and PyPlot are installed.  Execute within Julia `using IJulia, PyPlot`
  - this may install a lot of python packages via Conda and will take some time
  - if it fails or for more options, see https://github.com/JuliaPy/PyCall.jl
- restart the REPL and run `using IJulia; notebook(dir=pwd())`: this should open a window in your browser running the Jupyter notebook.
- probably you want to open and run `Products_run.ipynb` which runs through all the scripts in the right order.

If you struggle, reach out to the authors.

**To reproduce all results from raw data: run`Products_run` first**

It will run the all the notebooks in a specific order because all notebooks are not running independently (i.e. some notebooks need outputs from others to run). The results are exported in CSV files. The running order is the following:

0- `paths_download`

1- `helper-functions`

2- `Conductivity_calibration` and `Temperature_calibration`

3- `Lake_data`

4- `Temperature_and_Stage`

5- `Channel_geometry`

6- `P1_Discharges`, `P2..`, `P3..`

7- `Discharge_homogenization`

8- `Temperature_visualization`

9- `Hydraulic_thermodynamics_parameters`

10- `Raymond&Nolan2000_Theory`


**What the notebooks do ?**

**`helper-functions` store all the functions called in the others notebooks**

**`paths_download`**

   - defines the external path from where raw data will be downloaded from an online repository, where they are stored on your local machine and where the processed data will be stored while running the others notebooks (i.e. in your working environment).

**`Products_run`**

   - compiles all notebooks to re-produce the products (=data processed) on your local machine from raw-data download from `paths_download`.


**`Conductivity_calibration`**

   - in this notebook we calibrate our DCX22-CTD Keller sensors in order to get the salt concentration associated to the conductivity reading. We made 4 calibrations (2 in lab, 2 on the field). The calibration will be used to compute the discharge in `Discharges_Px`.

**`Temperature_calibration`**

   - here we calibrate the temperature of our DCX22-CTD Keller sensors with a 0°C bath in a cold room. The results are implemented in `helper-functions` to automatically correct temperature in `Temperature_and_stage`.

**`Channel_geometry`**

   - computes Figure 3: changes in channel bottom elevation at all stations during the lake drainage.
   - presents the daily water level cut at P4 and P5 from 15 to 31 July 2019.
   - presents the change in width at P3 and P5 during drainage.

**`lake_data`**

   - lake level time series $dh/dt$ from pressure datas is corrected and gathered together to create one correct time series.
   - lake volume is calculated thanks to swisstopo DEMs of the lake bassin acquired in 2018 and 2019 (the 2018 is chose).
   - lake input $Q_{in}$ is obtained from a temperature melt index model.
   - daily lake discharge $dV/dt$ is finally calculated.
   - lake volume times series and peak discharge for previous year are also computed for comparison (Fig. 2).
   - the hypsometry parametrization for the lake in 2019 is discussed.


**`Discharges_P1`**

   - explains how discharge is obtained at P1 in details, from CTD conductivity readouts by using salt dilution technique.

**`Discharges_P2`** calculates discharge at station P2 following the method explained in `Discharges_P1`.

**`Discharges_P3`** calculates discharge at station P2 following the method explained in `Discharges_P1`.

**`Temperature_and_stage`**

   - this notebook reads temperature and pressure from DCX22-CTD Keller sensor. The pressure is then converted in water stage.

   - pressure are corrected from atmospheric pressure on Fribourg's Plaine Morte station (2690m a.s.l) and 'suspicious values' are deleted (when sensors was out of water for exemple). All temperature are corrected according to `Temperature_calibration`, and the values when sensor were out are removed as well.

   - all temperature and stage time series are exported in CSV files at different time scale: raw time interval, hourly averaged and daily averaged.

**`Discharge_homogenization`**

   - reconstructs a continous channel discharge time series at P3 using a stage/discharge relation (Equation 4). Stage is imported from `Temperature_and_stage` and discharge from  `Discharges_Px`.


   - the data gap in daily discharge (because of a data gap in pressure time series) is filled by lake discharge using `Lake_data`. Then the gap in mean daily water stage is inverted using mean daily discharge and the stage/discharge relation described above.

   - uncertainties that arise from stage measurements, from salt dilution direct discharge measurements and from the power law fit between stage and discharge are propagated all along calculations using MonteCarloMeasurements.jl.

    - the results are presented at different temporal resolution (raw, hourly and daily).


**`Temperature_visualization`**

   - analyses the temperature decrease in canal along the drainage and we fit a exponential law.
   - computes Figure 7.
   - calculates the $e-folding length$.
   - calculates the Nusselt number from the spatial-cooling-rate (SCR) method.

**`Figure6`** computes the Figure 6.

**`Hydraulic_thermodynamics_parameters`**

   - calculates:
      - average stream velocities from tracer experiments over the differents channel segments (P5-P3, P5-P2, etc...).
      - average cross section (using direct discharges measurements)
      - Darcy-Weisbach Friction factor and Manning roughness (Table 3).
      - Reynold number.
      - Nusselt number (Fig. 8 and Fig. 9).

**`Raymond&Nolan2000_Theory`**

   - We investigate the theory of Raymond and Nolan 2000 about the lake area criterion for stable drainage with our data (their Equation 8).
