# Stitching neuropixel array files

## Requirements

Everything can be executed from matlab in arindal.

## Background

I use daniel o'shea's codebase in order to stitch multiple imec files to one another. This code adjusts gains across recording sessions. I think it's probably best that you stitch continguous lengths of data, but some unit tests suggest that you can get away with stitch files collected ~2-3 hours apart.

## Steps

1 . ssh into arindal, preferably as stimulus. Something like this will do
~~~~
ssh stimulus@arindal.cns.nyu.edu
cd /experiments/
~~~~

2 . Call matlab
~~~~
$ matlab9.4 -nodesktop -nosplash
~~~~

3 . Add path to neuropixel-utils

~~~~
>> addpath('/home/stimulus/neuropixel-utils/')

~~~~
4 . Load the channel map (the default works for all imros).

~~~~
>> channelMapFile = '/home/stimulus/neuropixel-utils/map_files/neuropixPhase3A_kilosortChanMap.mat'
~~~~

5 . Load each of your n ap.bin files into matlab using the Neuropixel.ImecDataset command, and group into list

~~~~
>> file1 = path_to_file1/file1.ap.bin;
>> file2 = path_to_file2/file2.ap.bin;
>> file_n = path_to_file_n/file_n.ap.bin;
>> imec1 = Neuropixel.ImecDataset(file1,'channelMap',channelMapFile);
>> imec2 = Neuropixel.ImecDataset(file2,'channelMap',channelMapFile);
>> imec_n = Neuropixel.ImecDataset(file_n,'channelMap',channelMapFile);
>> imec_list = {imec1,imec2,imec_n};

~~~~

6 . Set an outpath and write a stitched file
~~~~
>> outpath = '/path_to_output_file/output_filename.imec.ap.bin'
>> imecOut = Neuropixel.ImecDataset.writeConcatenatedFileMatchGains(imec_list,outpath)

~~~~
7 . imecOut stores details of the sorting. You can access these details again by calling the following.

~~~~
>> imecOut = Neuropixel.ImecDataset(outpath,'channelMap',channelMapFile);
>> important_meta_information = imecOut.readAPMeta();
~~~~

8 . The most important information is the length of the individual data that was stitched together which you will need finding which spikes went with which data runs. Find that with the follwoing.

~~~~
>> chunk_lengths = important_meta_information.concatenatedSamples;
>> [length1, length2, length_n]; %these data are in samples
~~~~
