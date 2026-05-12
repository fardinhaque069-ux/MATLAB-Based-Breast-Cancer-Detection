[file,path]=uigetfile('*.jpeg'); 

image=imread([path,file]); 

% Preprocessing 

grayImage = rgb2gray(image); 

filteredImage = medfilt2(grayImage); 

% Thresholding 

threshold = graythresh(filteredImage); 

binaryImage = imbinarize(filteredImage, threshold); 

% Morphological operations 

se = strel('disk', 5); 

closedImage = imclose(binaryImage, se); 

% Region of interest (ROI) extraction 

roiImage = imfill(closedImage, 'holes'); 

% Feature extraction 

stats = regionprops(roiImage, 'Area', 'Perimeter', 'Eccentricity', 'Solidity', 'Centroid'); 

% Simple threshold-based classification 

areas = [stats.Area]; 

eccentricities = [stats.Eccentricity]; 

% Define threshold values for classification 

areaThreshold = 100; 

eccentricityThreshold = 0.6; 

% Classify regions 

cancerousIndices = areas > areaThreshold & eccentricities > eccentricityThreshold; 

% Select centroids of cancerous regions 

cancerousCentroids = vertcat(stats(cancerousIndices).Centroid); 

% Visualization 

figure; 

subplot(2, 2, 1); 

imshow(image); 

title('Original Image'); 

subplot(2, 2, 2); 

imshow(grayImage); 

title('Gray Image'); 

subplot(2, 2, 3); 

imshow(filteredImage); 

title('Filtered Image'); 

subplot(2, 2, 4); 

imshow(roiImage); 

hold on; 

visboundaries(bwboundaries(roiImage)); 

% Plot cancerous centroids if any were found 

if ~isempty(cancerousCentroids) 

    plot(cancerousCentroids(:, 1), cancerousCentroids(:, 2), 'r*'); 

    disp(['Tumors are present, positively detected for breast cancer. Number of tumors: ' num2str(sum(cancerousIndices))]); 

else 

    disp('Tumors are not present, detected negative for breast cancer. Number of tumors: 0'); 

end 

title('Detected Regions'); 
