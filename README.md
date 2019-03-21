# Tutorial: Reproducing gravitational-wave observations with actual LIGO/Virgo data

The goal of these exercises is to recover a real signal, the gravitational wave event GW170814 that originated from the merger of two black holes, from the measured data of the LIGO and Virgo detectors. You will first get acquainted with the tools used for this data analysis and then apply them to the GW170814 event.

## Part 1: Setup

For this exercise we will rely on the Python programming language. Python is one of the most used, and most in demand, programming languages in the world (https://stackify.com/popular-programming-languages-2018/) and is well suited for most data analysis problems, both in physics and in the wider world. Please refer to our [setup tutorial](https://github.com/nilsleiffischer/jess-py-setup) on how to install a Python environment on your computer. Then, download this repository and open it in a Jupyter notebook environment. If you encounter any problems you can also use the online [Microsoft Azure](https://notebooks.azure.com) platform instead. In that case, clone this repository from within your Microsoft Azure account and click "Run".

## Part 2: Work through the provided notebooks

In this reposotory you find four notebooks, [1_CatalogData.ipynb](1_CatalogData.ipynb), [2_VisualizationSignalProcessing.ipynb](2_VisualizationSignalProcessing.ipynb), [3_WaveformMatchedFilter.ipynb](3_WaveformMatchedFilter.ipynb) and [4_ChisqSignificance.ipynb](4_ChisqSignificance.ipynb). We’ll work through each of these in turn.

[The first notebook](1_CatalogData.ipynb) will guide you through accessing the LIGO data. Each cell in the notebook contains a set of Python commands that can be run by pressing `<SHIFT> + <ENTER>`. Run through the cells one-by-one and read what is going on. An example is also given for how to make a plot within the notebook environment. When you are happy with what is going on move onto [the second notebook](2_VisualizationSignalProcessing.ipynb).

In the second notebook a demonstration of making the data plot for GW150914 is given. Run through this tutorial. You can attempt the challenge at the bottom, or directly move onto [the third notebook](3_WaveformMatchedFilter.ipynb) to have more time for the data analysis of GW170814.

In the third notebook is demonstrated how matched-filtering can be used to extract signals from noisy data. Again run through the notebook and attempt the challenge at the bottom or directly move onto the data analysis of GW170814. The fourth notebook is optional.

## Part 3: Find GW170814

Now we can put all of this knowledge together and write a new notebook to demonstrate extracting GW170814 from the data in the three LIGO and Virgo observatories.

### Task 1: Obtain the data

The LIGO Open Science Centre (https://losc.ligo.org/about/) contains data from observations made by LIGO and Virgo. You could download the data associated with GW170814 from there, but an easier way to access the data is with the `pycbc.catalog` module used in the Part 2 tutorials. The data we would like to analyze in the following is labeled "GW170814" in the catalog.

### Task 2: Extract the signal from the data

To analyze GW170814, we begin with following the same steps outlined in the Part 2 tutorials.

- First, In your Jupyter notebook, plot the data you have read in through `pycbc.catalog.Merger("GW170814")` as a function of time. Where is the peak of GW170814 in this data set? Can you see it?
- As demonstrated in the Part 2 tutorials, plot the PSD (sensitivity) of the three detectors. Which is the most and least sensitive instrument at this time?
- Also perform whitening and bandpassing (ca. 30 to 500 Hz) to extract the signal. Whitening is a process where the frequency-domain data is divided by the power-spectral density measured above, such that the resulting noise has equal power at all frequencies. How does the whitened data compare to the raw data?
- Plot the waveform and the spectrogram (qtransform). Can you see the chirp now? What is different to the merger events you analyzed before?

### Task 3: Matched-filtering

As discussed in the lecture, matched-filtering is the optimal method for extracting a known signal in Gaussian noise. We use a set of 25 gravitational waveform templates bundled in the file `waveform_bank.hdf5` that is included in this repository. The waveforms are stored in groups in the HDF5 file numbered from 0 to 24. In each of these groups is a data array named ‘hp’. This contains approximately 131000 sample points that represent a waveform. The sampling rate is 4096Hz so each of these waveforms is just over 32s long.

Extract the first waveform in this file (numbered `'0'`):

```python
import h5py

waveform_bank = h5py.File('waveform_bank.hdf5', 'r')
sp = pycbc.types.TimeSeries(waveform_bank['0']['hp'], delta_t=1/4096.)
hp = pycbc.types.TimeSeries(sp.numpy()[-32 * 4096:], delta_t=1/4096.)
```

Follow the third tutorial in Part 2 to matched-filter the data with this template as a function of time, and plot the signal-to-noise ratio as a function of time for the 3 instruments. Which instrument sees the signal loudest?

### Task 4: Visualization

Whiten and bandpass the template waveform and plot this on top of the whitened and bandpassed data such that the template lies on top of where you think GW170814 is in the data. How well do the two agree?

### Task 5: Alternative waveforms

Repeat the matched-filtering with the other templates in the waveform bank. Which waveform produces the largest signal-to-noise ratio? Is this true in all of the observatories?

### Task 6: Play GW170814 as a sound

Convert the band-passed and whitened data to a sound file. Can you hear the gravitational-wave signal? If not, try frequency shifting the data. Human ears are not so good at hearing at such low frequencies (and laptop speakers/basic headphones are not so good at playing such sounds!)

Hint:

```python
import IPython

data = pycbc.types.TimeSeries(waveform_bank['0']['hp'], delta_t=1/4096.)
IPython.display.Audio(data=data, rate=4096*2)
```
