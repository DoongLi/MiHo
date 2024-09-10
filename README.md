<h1 align="center"> 🧹🪣 MOP+MiHo+NCC 🖼️👀 <br> <br> Image Matching Filtering and Refinement <br> by Planes and Beyond </h1>

<p align="center">
  <a href="https://sites.google.com/view/fbellavia/home?authuser=0">Fabio Bellavia</a>
  ·
  <a href="https://ericzzj1989.github.io">Zhenjun Zhao</a>
  ·
  <a href="https://3dom.fbk.eu/people/profile/luca-morelli">Luca Morelli</a>
  ·
  <a href="https://3dom.fbk.eu/people/profile/remondino">Fabio Remondino</a>
</p>

<p align="center">
    <img src="https://github.com/fb82/MiHo/blob/main/data/other/lions.png" alt="example" width=80%>
    <br>
    <em>Planar clusters assigned by MOP+MiHo, outlier matches are marked with black diamonds.</em>
</p>

## What is it?
MOP+MiHo+NCC is a sequence of non-deep modules to filter and refine image matches:
1. Multiple Overlapping Planes (MOP) removes outlier matches while jointly clusters inlier matches into planes by an iterative RANSAC-based strategy;
2. Middle Homography (MiHo) improves MOP planar homography by minimizing the relative patch distortion in the plane reprojection;
3. Normalized Cross Correlation (NCC) refines keypoint position on the patches after the planar transformation.

## Is it good?
MOP+MiHo generally improves the matches when used as RANSAC pre-processing, not degrading the final results in any case. MOP+MiHo+NCC introduces additional improvements in the case of corner-like matches, which are predominant in [Key.Net](https://github.com/axelBarroso/Key.Net) or [SuperPoint](https://github.com/magicleap/SuperPointPretrainedNetwork), while in case of blob-like matches, predominant for instance in SIFT, unluckily it seems to degrade the results.

**Comparative evaluation is available [here](https://github.com/fb82/MiHo/tree/main/data/results/benchmark%20results/short)**.

Unlike other benchmarks, it is assumed that *no camera instrinsics are available* (the most general and maybe realistic case). In the tables $\text{AUC}^{F}_ \measuredangle$ means that pose is estimated by retrieving the essential matrix by adding camera intrinsics *after* fundamental matrix estimation, while $\text{AUC}^{E}_ \measuredangle$ indicates that the pose is estimated normalizing keypoint coordinates through intrinsics *before* directly computing the essential matrix by the 5-point method using `cv2.findEssentialMat`. AUCs considering metric instead of angular translation errors are reported as $\text{AUC}^{F}_ \square$ and $\text{AUC}^{E}_ \square$ in the case of [IMC PhotoTourism](https://www.kaggle.com/competitions/image-matching-challenge-2022/data), for which about 13K random image pairs have been chosen.

As better RANSAC, [MAGSAC](https://github.com/danini/magsac) is employed with the thresholds of 1 and 0.75 px, indicated respectively by $\text{MAGSAC}_ \uparrow$ and $\text{MAGSAC}_ \downarrow$. Ablation studies with further RANSAC implementations ([DegenSAC](https://github.com/ducha-aiki/pydegensac) and [PoseLib](https://github.com/PoseLib/PoseLib)) and thresholds are available [here](https://github.com/fb82/MiHo/tree/main/data/results/RANSAC%20ablation%20results/short). Unlike defaults, for all RANSAC implementation the maximum number of iterations was incremented to 2000 for better performances.

## How to use?
Just check comments and run `miho_ncc_demo.py`. Notice that in the code MOP is denoted as `miho_unduplex` while MOP+MiHo as `miho_duplex`; in case you get Qt related errors, try to uncomment `matplotlib.use('tkagg')` in `src/miho.py`.

Full benchmark can be run with `run_bench.py` and tables built with `save_bench.py`; commenting specific lines in `pipes`, `pipe_heads`, `pipe_ransacs` and `benchmark_data` can restrict the compared methods and evaluated datasets. For the RANSAC ablation the analogous scripts are `run_ransac_ablation.py` and `save_ransac_ablation.py`. The additional scripts `intrinsiscs_bench.py` and `intrinsics_other_bench.py` provide further reports about camera intrinsics, while `corr_bench.py` outputs the correlation between the different measurement errors. Complete detailed results can be found inside `data/results`. 


## Where can I find more details?
The early idea of the approach can be found in the paper [Progressive keypoint localization and
refinement in image matching (FAPER 2023)](https://drive.google.com/file/d/1sxQOpbBTvvqnpfR98HGHQafquww6JuaK/edit), *further details will be available in an upcoming paper*.
