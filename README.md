Content-Aware Image Resizing
Overview
This project implements the Seam Carving algorithm for content-aware image resizing. Unlike traditional resizing methods that uniformly scale an image, content-aware resizing preserves important image features by selectively removing or inserting pixels with the least impact on the visual content.

What is Seam Carving?
Seam carving is an algorithm for intelligently resizing images without cropping them or distorting important visual elements. It works by:

Computing an "energy map" that identifies areas of visual importance in the image
Finding the lowest-energy "seams" (connected paths of pixels) through the image
Removing or duplicating these seams to reduce or expand the image dimensions
This approach allows for:

Resizing images while preserving important content
Removing unnecessary or distracting elements
Adjusting aspect ratios with minimal distortion
Features
Content-Aware Resizing: Resize images while preserving important visual content
Energy Function: Implementation of gradient magnitude energy calculation
Seam Detection: Dynamic programming approach to identify optimal seams
Seam Removal/Insertion: Support for both image reduction and expansion
Visual Feedback: Option to visualize the seams being removed or added
Implementation
This project is implemented in MATLAB and includes the following key components:

content_aware_image_resizing.m: Main script for the seam carving algorithm
Energy calculation functions
Seam identification using dynamic programming
Functions for seam removal and insertion
Visualization utilities to display the algorithm's progress
Requirements
MATLAB (R2018b or newer recommended)
Image Processing Toolbox
Usage
Clone the repository:

Copygit clone https://github.com/EyadEhab/Content-Aware-Resize-Images.git
cd Content-Aware-Resize-Images
Open MATLAB and navigate to the project directory

Run the main script with your desired parameters:

Copy% Example usage
input_image = imread('path/to/image.jpg');
output_image = content_aware_resize(input_image, new_width, new_height);
imshow(output_image);
How It Works
Energy Function
The algorithm first calculates an energy function for each pixel, which quantifies how important that pixel is to the image's visual content. This is typically done using gradient magnitude:

Copyfunction energy = compute_energy(image)
    % Convert to grayscale if needed
    if size(image, 3) == 3
        gray_image = rgb2gray(image);
    else
        gray_image = image;
    end
    
    % Compute x and y gradients
    [Gx, Gy] = imgradientxy(gray_image);
    
    % Energy is the sum of absolute gradients
    energy = abs(Gx) + abs(Gy);
end
Seam Detection
A seam is identified using dynamic programming to find the path of minimum energy from one side of the image to the other:

Copyfunction [seam] = find_vertical_seam(energy)
    % Get image dimensions
    [h, w] = size(energy);
    
    % Initialize cumulative energy map
    cum_energy = energy;
    
    % Compute cumulative minimum energy
    for i = 2:h
        for j = 1:w
            if j == 1
                cum_energy(i, j) = energy(i, j) + min(cum_energy(i-1, j), cum_energy(i-1, j+1));
            elseif j == w
                cum_energy(i, j) = energy(i, j) + min(cum_energy(i-1, j-1), cum_energy(i-1, j));
            else
                cum_energy(i, j) = energy(i, j) + min([cum_energy(i-1, j-1), cum_energy(i-1, j), cum_energy(i-1, j+1)]);
            end
        end
    end
    
    % Backtrack to find the seam
    seam = zeros(h, 1);
    [~, seam(h)] = min(cum_energy(h, :));
    
    for i = h-1:-1:1
        j = seam(i+1);
        if j == 1
            [~, ind] = min([cum_energy(i, j), cum_energy(i, j+1)]);
            seam(i) = j + ind - 1;
        elseif j == w
            [~, ind] = min([cum_energy(i, j-1), cum_energy(i, j)]);
            seam(i) = j + ind - 2;
        else
            [~, ind] = min([cum_energy(i, j-1), cum_energy(i, j), cum_energy(i, j+1)]);
            seam(i) = j + ind - 2;
        end
    end
end
Seam Removal/Insertion
The algorithm then removes or duplicates the identified seams to achieve the desired dimensions.

Results
The algorithm produces visually pleasing results when resizing images with complex content. Below are some examples:

[Sample results would typically be shown here with before/after comparisons]

References
Avidan, S., & Shamir, A. (2007). "Seam Carving for Content-Aware Image Resizing". ACM Transactions on Graphics, 26(3), Article 10.
Rubinstein, M., Shamir, A., & Avidan, S. (2008). "Improved Seam Carving for Video Retargeting". ACM Transactions on Graphics, 27(3), Article 16.
License
This project is licensed under the MIT License - see the LICENSE file for details.

Acknowledgements
The original Seam Carving algorithm by Shai Avidan and Ariel Shamir
MATLAB and its Image Processing Toolbox
Author
Eyad Ehab