---
layout: page
title: GraphSLAM with ICP
description: two SLAM approaches based on the laser scan correlation method named Iterated Closest Point (ICP)
img: assets/img/projects/ICP/frontpage.png
pdf: 
importance: 3
category: work
related_publications:
---

# Introduction

In this practical work, we will work on two SLAM approaches based on the laser scan correlation method named Iterated Closest Point (ICP).

# Incremental SLAM

*Q: By looking at the code and at the course, explain in details what is the algorithm implemented in this script. In particular explain what is the scan used as a reference to compute a new scan position. Highlight the difference with the ICP localisation proposed in the first ICP practical work.*

The algorithm in the program is that before append one new scan into our constructing map, calculate its distance with all the previous poses of scans, and sort the scans according to the distances. Perform ICP with closest scan and calculate the distance between the closest scan pose and the new scan pose, if the distance is larger than a threshold, accept this scan.

<div class="row">
    <div class="col-sm mt-12 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPIncrementalSLAM.png" title="ICP Incremental SLAM" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

    
The reference of the scan is the closest scan from the previous scans accepted by our map.

*Q: When a loop is closed in the environment, explain what is corrected or not in the localisation and in the map.*

When the loop is closed, only the last pose that closes the loop (A.K.A the pose with the same localization) will be corrected, also the corresponding scanning map for that pose. Nothing else will be corrected because this method doesn't update other scans or the map(without backtracking).

*Q: What are the role of the step and distThresholdAdd parameters ? Explain what happens for each of them when they are decreased or increased. What eventually happens when they are too large ? Suggest a reasonable compromise for their values.*

The step defines how frequency we pick the scans from the data set, it means we pick only one scan by one step. The distThresholdAdd defines the minimal distance between the scans in the map, it means too closed scans will not be accepted.

If only the step decreases, the number of scans taken is larger, the running time will increase. However, because the distThresholdAdd doesn't allow the too closed scans, the scans accepted by the map may not change. If the step increases, the number of scans in the scan list decreases, so the data will be more sparse and the result is not precise and uncorrected with a too-large step. In that case, distThresholdAdd may have less and less influence because the scans taken in the list are already sparse enough. 

If only distThreshold decreases, the number of scans added becomes larger until the maximum. If the distThreshold increases, the scans added in the map will decrease.

As we have discussed above, if step or distThresholdAdd become too large, the scans taken in the list and the scans added in the map will be few, and there is not enough data to construct our map. To compromise these two values, we need to make sure there are enough scans calculated by ICP method, so the step can not be too large, we could take it as 3~5. And for a reasonable distThresholdAdd, we need to make sure it makes sense, which means we need to watch if there is an obvious filtering effect after applying it, but also make sure the rest scans are precise enough.


<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPIncrementalSLAM0.2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPIncrementalSLAM0.5.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

# Graph SLAM

The algorithm: In this program, we have two parts - build the graph and update/optimize it. At first, to construct the map, after getting list of map scan sorted by distance, we perform ICP with closest scan to correct future odometry and add the scan if it is not too closed with the previous ones. But it does one more step for build the graph - adding edges: keep only the scans below a distance threshold(from one current scan), or the closest one, then compute position of new scan with respect to the reference scan, and create a corresponding relative pose if the error is small and add one edge to the graph. Note that, in this step, we take the reference scan among the closest map scans(previous).

<div class="row">
    <div class="col-sm mt-12 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPGraphSLAM.png" title="ICP Graph SLAM" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

When the graph is built with the relative pose, we need to optimize it, which means each scan pose from its neighbors is recomputed. We create a list of poses for one scan through neighbors and relative positions and and take the mean of the pose list as the new pose. Take the largest error as the update value, then stop the process until the updates fall below a threshold.

**Compared with the incremental SLAM method, when constructing the map, the new algorithm keeps updating the pose according to the nearest poses in the map and the relative position of the new scan, while the previous algorithm directly accepts the result of ICP method.**

When a loop is closed, the map/wall is apparently corrected and every pose/trajectory is corrected too, because every scan in the map is updated during the optimization process. It has the capacity of backtracking(retour en arrière).

*Q: What are the role of the distThresholdAdd and distThresholdMatch parameters ? Explain what happens for each of them when they are decreased or increased. What happens when distThresholdAdd > distThresholdMatch? Suggest a reasonable compromise.*

The distThresholdAdd is still the threshold of distance to add scan into the map. It defines the minimal distance between the scans in the map, it means too closed scans will not be accepted. The distThresholdMatch is a distance threshold used to chose some closed poses which are already in the map, and used to update the graph. The relative pose will be recomputed according to the poses, whose distance with current new scan pose is below distThresholdMatch.

If only distThresholdAdd decreases, the number of scans added becomes larger until the maximum. If the distThresholdAdd increases, the scans added to the map will decrease.

If only distThresholdMatch decreases, there will be less pose in the list used to calculate the relative pose. If it increases, it means the reference poses for the relative pose increase, and the relative pose may be more reasonable.

Once distThresholdAdd > distThresholdMatch, then the relative pose of each new scan will only calculate based on the closest one pose. The performance is limited.

To choose a reasonable compromise of these two parameters, we need to keep distThresholdAdd smaller than distThresholdMatch. At the same time, a too-small distThresholdAdd causes some unnecessary steps that are too close and a too-big distThresholdMatch makes no sense because those far poses have no meaning of reference for the relative pose. To calculate the relative pose, it is better to have 1-2 nearest poses as the reference, so it is better to take the distThresholdMatch around 2 times larger than distThresholdAdd. To make it clearer, we do the tests below.

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPGraphSLAM0.1.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPGraphSLAM0.5.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPGraphSLAM0.6.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/projects/ICP/ICPGraphSLAM1.0.png" title="" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

