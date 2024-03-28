# vehicle-number-plate-detector-using-matlab

% Step 1: Read the image and extract the license plate area
a = imread(uigetfile('C:\Users\roshan james\OneDrive\Desktop'));
a = rgb2gray(a);
[r, c, ~] = size(a);
b = a(r/3:r, 1:c);

% Step 2: Threshold and filter the license plate area
b_thresholded = b > 150;
b_filtered = medfilt2(b_thresholded, [4 4]);
b_filtered = medfilt2(b_filtered, [4 4]);
b_filtered = medfilt2(b_filtered, [4 4]);
b_filtered = medfilt2(b_filtered, [5 5]);
b_filtered = medfilt2(b_filtered, [5 5]);

% Step 3: Fill holes and label regions
BW_filled = bwfill(b_filtered, 'holes');

% Step 4: Noise removal and region labeling
BW_filtered = medfilt2(BW_filled, [4 4]);
[L, num] = bwlabel(BW_filtered);
STATS = regionprops(L, 'Area');

% Step 5: Analyze orientation of candidate regions
cc = [];
removed = 0;
for i = 1:num
    dd = STATS(i).Area;
    cc(i) = dd;
    if (dd < 50000)
        L(L == i) = 0;
        removed = removed + 1;
        num = num - 1;
    end
end

[L2, num2] = bwlabel(L);
STATS = regionprops(L2, 'All');

if num2 > 2
    for i = 1:num2
        aa = STATS(i).Orientation;
        if aa > 0
            imshow(L == i);
        end
    end
    disp('exit');
end

% Step 6: Extract the license plate
B = STATS.BoundingBox;
Xmin = B(2);
Xmax = B(2) + B(4);
Ymin = B(1);
Ymax = B(1) + B(3);
LP = b(Xmin + 25:Xmax - 20, Ymin + 10:Ymax - 10);
figure;
imshow(LP, []);
