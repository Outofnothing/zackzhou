convert matlab calibration result to opencv-yaml


    % read parameters from .mat files(left pair and right pair cameras) 
    params = load('left.mat');
    stereo = params.stereoParams;
    fileID = fopen('build/calib_params.yml', 'w');
    % write header
    fprintf(fileID, "%s\n%s\n", "%YAML:1.0", "---");
    % write distortion
    dist(:,1) = [stereo.CameraParameters1.RadialDistortion stereo.CameraParameters1.TangentialDistortion];
    dist(:,2) = [stereo.CameraParameters2.RadialDistortion stereo.CameraParameters2.TangentialDistortion];
    for c=1:2
        m = dist(:,c);
        fprintf(fileID, "%s%d%s\n   %s\n   %s\n   %s\n   %s", "Distortion", c-1,": !!opencv-matrix", "rows: 4", "cols: 1", "dt: f", "data: [");
        for i=1:3
            fprintf(fileID, "%.9e, ", m(i));
        end
        fprintf(fileID, "%.9e]\n", m(4));
    end 


    % write intrinsic matrixes
    Intri(:,:,1) = stereo.CameraParameters1.IntrinsicMatrix;
    Intri(:,:,2) = stereo.CameraParameters2.IntrinsicMatrix;
    for c=1:2
        fprintf(fileID, "%s%d%s\n   %s\n   %s\n   %s\n   %s", "cameraMatrix", c-1,": !!opencv-matrix", "rows: 3", "cols: 3", "dt: f", "data: [");
        m = Intri(:,:,c).';
        for row=1:3
            for col=1:3
                fprintf(fileID, "%.9e", m(row,col));
                if row<3 || col<3
                    fprintf(fileID, ", "); % cannot add "," after last element
                end
            end
        end
        fprintf(fileID, "]\n");
    end
    % translation of camera2 in left and right camera pair
    Translation(:,:,1) = stereo.TranslationOfCamera2;
    for c=1:1
        fprintf(fileID, "%s%d%s\n   %s\n   %s\n   %s\n   %s", "Translation", c-1,": !!opencv-matrix", "rows: 3", "cols: 1", "dt: f", "data: [");
        m = Translation(:,:,c);
        for col=1:3
            fprintf(fileID, "%.9e", m(col));
            if col<3
                fprintf(fileID, ", "); % cannot add "," after last element
            end
        end
        fprintf(fileID, "]\n");
    end
    % rotation of camera2 in left and right camera pair
    Rotation(:,:,1) = stereo.RotationOfCamera2.';
    for c=1:1
        fprintf(fileID, "%s%d%s\n   %s\n   %s\n   %s\n   %s", "Rotation", c-1,": !!opencv-matrix", "rows: 3", "cols: 3", "dt: f", "data: [");
        m = Rotation(:,:,c);
        for row=1:3
            for col=1:3
                fprintf(fileID, "%.9e", m(row,col));
                if row<3 || col<3
                    fprintf(fileID, ", "); % cannot add "," after last element
                end
            end
        end
        fprintf(fileID, "]\n");
    end
    fclose(fileID);

Read calibration params from yml

    cv::FileStorage fs_calib("calib_params.yml", cv::FileStorage::READ);
	if (fs_calib.isOpened())
	{
		for (int camera = 0; camera < 2; camera++)
		{
			
			// IntrinsicMatrix generated in matlab must be transposed to use in opencv（data in yml file is already transposed)
			char* str = new char[strlen("cameraMatrix")+1];
			sprintf(str, "%s%d", "cameraMatrix", camera);
			fs_calib[str] >> cameraMatrix[camera];
			// OpenCV's distortion coefficient vector:  the radial parameters come first; these are followed by the
			// two tangential parameters --LearnOpenCV3 Chapter19 Page724
			// this problem is handled in matlab script "save_camera_calib_to_yml.m"
			str = new char[strlen("Distortion") + 1];
			sprintf(str, "%s%d", "Distortion", camera);
			fs_calib[str] >> distorCoeff[camera];
			// use CV_64F type for inputs of function of stereoRectify
			distorCoeff[camera].convertTo(distorCoeff[camera], CV_64F);
			std::cout << "Camera " << camera << " Matrix: \n" << cameraMatrix[camera] << std::endl;
			std::cout << "Camera " << camera << " distortion vector: \n" << distorCoeff[camera] << std::endl;
		}
		// initialize rotation and translation matrix for left and right camera set
		// the matlab matrix need to be transposed to fit opencv
		// use CV_64F type for inputs of function of stereoRectify
		
		fs_calib["Rotation0"] >> rotationMat;
		rotationMat.convertTo(rotationMat, CV_64F);
		std::cout << "Camera rotation Matrix: \n" << rotationMat << std::endl;
		// use CV_64F type for inputs of function of stereoRectify
		translationMat = cv::Mat(3, 1, CV_64F);
		fs_calib["Translation0"] >> translationMat;
		translationMat.convertTo(translationMat, CV_64F);
		std::cout << "Camera translation Matrix: \n" << translationMat << std::endl;
	}