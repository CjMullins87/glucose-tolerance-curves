# Describing Glycemic Control Using Data from a FreeStyle Libre 3

FreeStyle Libre provides an in-house reporting tool which allows continuous
glucose monitor (CGM) users the ability to build standardized reports for the
consumption of users and healthcare professionals.

These reports offer great insights that can drive treatment decisions, such as an
estimated A1C; hourly average glucose readings; and a composite glucose graph
which can help describe what ranges patients typically spend their time in.

This data is great for describing glycemic control, but not glucose tolerance. With
access to CGMs - and thus high resolution blood glucose data - expanding through
over-the-counter programs and broad insurer coverage, we should seek methods to estimate
or describe glucose tolerance based on available data.

# Output and Method

Using Python and the data from my CGM - and borrowing the visual language of
the FreeStlye Libre reports - I built high-resolution descriptive glucose tolerance
curves and calculated the area under the median in `mgH/L`:

![image](images/May&June2024.png)

![image](images/July&Aug2024.png)

To estimate glucose tolerance, I relied on two key features of my glucose data:

* An assumption that there's a 'threshold' glucose reading with is directly
attributable to a dietary choice.
* And an assumption that I would consistently fall below that threshold in
between meals

Using these assumptions, it's fairly straightforward to walk an ordered timeseries of
glucose data and look for a 'spike' as determined by the threshold, and then take
measurements of glucose data oriented around that spike.

The method sequentially walks such a timeseries until it hits a trigger reading (`center`),
and then it slices a given `n` readings before and `n` readings after. These slices are
then assembled into a matrix of same-shape-same-center arrays, allowing me to determine
quantile-level descriptions of blood glucose at each `n` step in time.

Given that the data served by the FSL3 is usually an ordered, monotonic
timeseries, `scikitlearn` is used to calculate an AUC for the median curve. The AUC
can then be used to assess glucose tolerance, where a lower AUC is indicative
of better tolerance.

# How to build your own

I've got an example notebook in `docs`, and the method itself in
`src/walking_curves.py`. It relies on `numpy`, `pandas`, and `scikitlearn` for
calculating the AUC.

From your personal database, or more likely LibreView, you need to extract an
ascending-order timeseries of your glucose readings. Then, just pass the array into
the method with the parameters of your choice.

The method does **not** generate pretty graphs, but I've included the code I used
to assemble the graphs for user reference.