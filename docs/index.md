# Lesion Spotlight

![project logo](assets/LesionSpotlight.png){ width="150" .center}

Welcome to the Lesion Spotlight Slicer Extension!

This extension provides image segmentation and local image contrast enhancement approaches in order to detect abnormal white matter voxels signals in magnetic resonance images. At moment, there are available the:

- `LS Segmenter` (specific for hyperintense Multiple Sclerosis lesion segmentation on T2-FLAIR images),
- `LS Contrast Enhancement` (specific to increase the contrast of abnormal voxels of T2-FLAIR images) 
- `AFT Segmenter` as a simple implementation of a recent automatic Multiple Sclerosis (MS) lesion segmentation approaches[^1]. 

!!! note 
    The `LS Segmente` module implements a T2-FLAIR hyperintense lesion segmentation based on the algorithm published in the paper[^2]


[^1]: Cabezas, M., Oliver, A., Roura, E., Freixenet, J., Vilanova, J. C., Ramió-Torrentà, L., Rovira, À. and Lladó, X. (2014) ‘Automatic multiple sclerosis lesion detection in brain MRI by FLAIR thresholding’, Computer Methods and Programs in Biomedicine. Elsevier Ireland Ltd, 115(3), pp. 147–161. DOI: 10.1016/j.cmpb.2014.04.006.

[^2]: da Silva Senra Filho, A. C. (2017) ‘A hybrid approach based on logistic classification and iterative contrast enhancement algorithm for hyperintense multiple sclerosis lesion segmentation’, Medical & Biological Engineering & Computing. doi: 10.1007/s11517-017-1747-2.


!!! info
    The `Logistic Contrast Enhancement`, `Weighted Enhancement Image Filter` and `Automatic FLAIR Threshold` modules are only supporting CLI methods added in the extension in order to calculate specific parts of the segmentation procedure. For this reason, these modules are not supposed to be used alone. Please, use the `LS Contrast Enhancer` or `LS Segmenter` modules only.

 
## Use Cases

Most frequently used for these scenarios:

- **Use Case 1:** Hyperintense Multiple Sclerosis (MS) lesions segmentation

    - T2-FLAIR images are usually applied to MS diagnosis in order to detect hyperintense MS lesions. In this case, the `LS Segmenter` module can be useful.

- **Use Case 2:** Increase contrast in abnormal voxels in white matter tissue

    - There are some lesion segmentation approaches that rely on the voxel intensity level presented in the white matter tissue, where the `LS Contrast Enhancer` module can be helpful to increase the contrast between lesions and surrounding brain tissues (mainly normal-appearing white matter - NAWM).


![MS lesions and White Matter 3D representation](assets/Lesion3DRender.png)

MS lesions and White Matter 3D representation

## Modules description and usage

### LS Segmenter

![logo-ls-segmenter](assets/LSSegmenter.png)

This module offers a hybrid algorithm based on the iterative contrast enhancement (ICE) and logistic classification methods for hyperintense Multiple Sclerosis lesion segmentation task.

#### Painel and Parameters

**IO:**

- T1 Volume
- T2-FLAIR Volume
- Lesion Label

    - Output a global lesion mask

- Is brain extracted?

    - Is the input data (T1 and T2-FLAIR) already brain extracted?

- Apply noise attenuation step

    - Apply noise attenuation based on the [Anisotropic Anomalous Diffusion](https://anomalousfiltersextension.readthedocs.io/en/latest/) algorithm on the input data (T1 and T2-FLAIR)

- Apply bias field correction step

    - Apply bias field correction based on the N4ITK Bias Field Correction algorithm on the input data (T1 and T2-FLAIR)

**Noise Attenuation Parameters:**

- Conductance

    - The conductance regulates the diffusion intensity in the neighbourhood area. Choose a higher conductance if the input image has strong noise seem in the whole image space.

- Number Of Iterations

    - The number of iterations regulates the numerical simulation of the anomalous process over the image. This parameters is also related with the de-noising intensity, however it is more sensible to the noise intensity. Choose the higher number of iterations if the image presents high intensity noise which is not well treated by the conductance parameter

- Q Value
    
    - The anomalous parameter (or q value) is the generalization parameters responsible to give the anomalous process approach on the diffusion equation. See the reference paper[1] to choose the appropriate q value (at moment, only tested in MRI T1 and T2 weighted images)

**Registration Parameters: (based on BRAINSFit module)**

- Percentage Of Samples

    - Percentage of voxel used in registration

- Initiation Method

    - Initialization method used for the MNI152 registration

- Interpolation

    - Choose the interpolation method used to register the standard space to input image space. Options: Linear, NearestNeighbor, B-Spline

**Segmentation Parameters:**

- Absolute Error Threshold

    - Define the absolute error threshold for gray matter statistics. This measure evaluated the similarity between the MNI152 template and the T2-FLAIR gray matter fluctuation estimative. A higher error gives a higher variability in the final lesion segmentation

- Gamma

    - Define the outlier detection based on units of standard deviation in the T2-FLAIR gray matter voxel intensity distribution

- White Matter Matching

    - Set the local neighborhood searching for label refinement step. This metric defines the percentage of white matter tissue that surrounds the hyperintense lesions. Higher values defines a conservative segmentation

- Minimum Lesion Size
  
    - Set the minimum lesion size adopted as a true lesion in the final lesion map. Units are given in number of voxels

- Gray Matter Mask Value

    - Set the mask value that represents the gray matter. Default is defined based on the (Basic Brain Tissues module) output

- White Matter Mask Value

    - Set the mask value that represents the white matter. Default is defined based on the (Basic Brain Tissues module) output

![T2-FLAIR image from a MS patient](assets/T2FLAIR_patient.png){ width="600" .center}

T2-FLAIR image from a MS patient

![T2-FLAIR hyperintense lesion segmentation provided by the LS Segmenter module](assets/T2FLAIR_patient_lesionLabel.png){ width="600" .center}

T2-FLAIR hyperintense lesion segmentation provided by the `LS Segmenter` module

### LS Contrast Enhancer

![logo-ls-contrast-enhancer](assets/LSContrastEnhancer.png)

#### Painel and Parameters

**Input/Output Parameters:**

- Input Volume
- Output Volume

    - Output enhanced volume

**Noise Attenuation Parameters:**

- Conductance

    - The conductance regulates the diffusion intensity in the neighbourhood area. Choose a higher conductance if the input image has strong noise seem in the whole image space.

- Number Of Iterations

    - The number of iterations regulates the numerical simulation of the - anomalous process over the image. This parameters is also related with the de-noising intensity, however it is more sensible to the noise intensity. Choose the higher number of iterations if the image presents high intensity noise which is not well treated by the conductance parameter

- Q Value

    - The anomalous parameter (or q value) is the generalization parameters responsible to give the anomalous process approach on the diffusion equation. See the reference paper[1] to choose the appropriate q value (at moment, only tested in MRI T1 and T2 weighted images)

**Registration Parameters: (based on BRAINSFit module)**

- Is brain extracted

    - Is the input data (T1 and T2-FLAIR) already brain extracted?

- Percentage Of Samples

    - Percentage of voxel used in registration

- Initiation Method

    - Initialization method used for the MNI152 registration

- Interpolation

    - Choose the interpolation method used to register the standard space to input image space. Options: Linear, NearestNeighbor, B-Spline

**Lesion Enhancement Function Parameters:**

- Weighting Enhancement

    - Weighting enhancement value, in percentage, that will be used to increase the lesion signal in the image

- Threshold Method

    - Choose the threhsold method for the lesion enhancement procedure. Options: MaximumEntropy, Otsu, Moments, Intermodes and IsoData

- Number Of Bins

    - Number Of Bins for the histogram calculation

- Flip Object

    - Flip object in the image. This inform if the dark part of the histogram that should be enhanced

- Label Map Threshold

    - Threshold for the lesion label map

![T2-FLAIR image with original lesion contrast](assets/T2FLAIR_beforeContrast.png){ width="600" .center}

T2-FLAIR image with original lesion contrast

![T2-FLAIR image with hyperintense MS lesion contrast enhanced by the LS Contrast Enhancer module](assets/T2FLAIR_afterContrast.png){ width="600" .center}

T2-FLAIR image with hyperintense MS lesion contrast enhanced by the LS Contrast Enhancer module

### AFT Segmenter

![aft-logo](assets/AFTSegmenter.png)

This module offers an implementation of a recent Multiple Sclerosis lesion segmentation approach based on a unsupervised method described by Cabezas et al. [^1]. This module is intended to be used with FLAIR and T1 MRI volumes, which the MS lesions can be detected.

#### Painel and Parameters

**IO:**

- T1 Volume
  
    - Input T1 volume

- T2-FLAIR Volume
  
    - Input T2-FLAIR volume

- Lesion Label
  
    - Output a global lesion mask

- Is brain extracted?
  
    - Is the input data (T1 and T2-FLAIR) already brain extracted?

**Segmentation Parameters:**

- Absolute Error Threshold

    - Define the absolute error threshold for gray matter statistics. This measure evaluated the similarity between the MNI152 template and the T2-FLAIR gray matter fluctuation estimative. A higher error gives a higher variability in the final lesion segmentation

- Gamma

    - Define the outlier detection based on units of standard deviation in the T2-FLAIR gray matter voxel intensity distribution

- White Matter Matching

    - Set the local neighborhood searching for label refinement step. This metric defines the percentage of white matter tissue that surrounds the hyperintense lesions. Large values defines a conservative segmentation, i.e. in order to define a true MS lesion, it must be close to certain percentage of white matter area.

- Minimum Lesion Size

    - Set the minimum lesion size adopted as a true lesion in the final lesion map. Units are given in number of voxels

- Gray Matter Mask Value

    - Set the mask value that represents the gray matter. Default is defined based on the (Basic Brain Tissues module) output

- White Matter Mask Value

    - Set the mask value that represents the white matter. Default is defined based on the (Basic Brain Tissues module) output

![Input T2-FLAIR image with Multiple Sclerosis lesions](assets/T2FLAIR_patient_AFT.png){ width="600" .center}

Input T2-FLAIR image with Multiple Sclerosis lesions

![Lesion map resulted from AFT Segmenter module](assets/T2FLAIR_patient_lesionLabel_AFT.png){ width="600" .center}

Lesion map resulted from AFT Segmenter module

## Cite this tool

We hope that the `Lesion Spotlight` can be helpful for your applications. If possible, recall to cite at least one of the following publications:

* da Silva Senra Filho, A. C. (2017) ‘A hybrid approach based on logistic classification and iterative contrast enhancement algorithm for hyperintense multiple sclerosis lesion segmentation’, Medical & Biological Engineering & Computing. doi: [10.1007/s11517-017-1747-2](https://doi.org/10.1007/s11517-017-1747-2).


## License

This project is under Apache Version 2.0, license and following details are given at the [LICENSE](https://github.com/CSIM-Toolkits/Slicer-LesionSpotlightExtension/blob/main/LICENSE) file in the project repository.