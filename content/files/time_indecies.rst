.. _timeindeciesFile:

Time Indecies File
==================

The time indecies file is used when we want the inversion to do the following:

	1. Invert the data for an earlier set of time channels
	2. Use the corresponding recovered model as a starting model for an inversion that also includes later time channels

This process can be repeated multiple times for a single run of the code. Let us consider a data set that contains data at 10 time channels. We will invert the first 4 time channels, then use the recovered model in an inversion that inverts the first 7 time channels. The second recovered model will be used as a starting modeling for an inversion that inverts all time channels. Consequently, 3 inversions are run during one execution of the code. In this case, the time indecies file would be a text file formatted as follows:

|
| 3                          ! # of lines
| 1 2 3 4                    ! invert earliest 4
| 1 2 3 4 5 6 7              ! invert earliest 7
| 1 2 3 4 5 6 7 8 9 10       ! invert earliest 10
|
|








