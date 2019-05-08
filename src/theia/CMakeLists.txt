# Copyright (C) 2013 The Regents of the University of California (Regents).
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are
# met:
#
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#
#     * Redistributions in binary form must reproduce the above
#       copyright notice, this list of conditions and the following
#       disclaimer in the documentation and/or other materials provided
#       with the distribution.
#
#     * Neither the name of The Regents or University of California nor the
#       names of its contributors may be used to endorse or promote products
#       derived from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDERS OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
# Please contact the author of this library if you have any questions.
# Author: Chris Sweeney (cmsweeney@cs.ucsb.edu)

# Brutally modified by Tom Paynter to make building for iOS easier.
# cmake .. -DBUILD_LIB_theia_sfm=ON -DINCLUDE_DIRECTORIES="/usr/include/eigen3/\;/usr/include/suitesparse/"

cmake_minimum_required(VERSION 3.11)

project(theia
        VERSION 0.7.0
        DESCRIPTION "TheiaSfM library.")        

option(BUILD_LIB_theia_image    "Build the image library."    OFF)
option(BUILD_LIB_theia_io       "Build the io library."       OFF)
option(BUILD_LIB_theia_matching "Build the matching library." OFF)
option(BUILD_LIB_theia_math     "Build the math library."     OFF)
option(BUILD_LIB_theia_sfm      "Build the sfm library."      OFF)
option(BUILD_LIB_theia_solvers  "Build the solvers library."  OFF)
option(BUILD_LIB_theia_util     "Build the util library."     OFF)

option(INCLUDE_DIRECTORIES "Include directories for external libraries.")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

#Prepend include directories local to this repo.
list(INSERT 
  INCLUDE_DIRECTORIES 
  0
  ..
  ../../libraries/
  ../../libraries/cereal/include/)

if(BUILD_LIB_theia_image)
  add_library(theia_image 
    image/descriptor/akaze_descriptor.cc
    image/descriptor/create_descriptor_extractor.cc
    image/descriptor/descriptor_extractor.cc
    image/descriptor/sift_descriptor.cc
    image/image_cache.cc
    image/image.cc
    image/keypoint_detector/sift_detector.cc)
  target_include_directories(theia_image PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_image ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_image)

if(BUILD_LIB_theia_io)
  add_library(theia_io
    io/import_nvm_file.cc
    io/populate_image_sizes.cc
    io/read_1dsfm.cc
    io/read_bundler_files.cc
    io/read_calibration.cc
    io/read_keypoints_and_descriptors.cc
    io/read_strecha_dataset.cc
    io/reconstruction_reader.cc
    io/reconstruction_writer.cc
    io/sift_binary_file.cc
    io/sift_text_file.cc
    io/write_bundler_files.cc
    io/write_colmap_files.cc
    io/write_keypoints_and_descriptors.cc
    io/write_nvm_file.cc
    io/write_ply_file.cc)
  target_include_directories(theia_io PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_io ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_io)

if(BUILD_LIB_theia_matching)
  add_library(theia_matching
    matching/brute_force_feature_matcher.cc
    matching/cascade_hasher.cc
    matching/cascade_hashing_feature_matcher.cc
    matching/create_feature_matcher.cc
    matching/feature_matcher_utils.cc
    matching/feature_matcher.cc
    matching/fisher_vector_extractor.cc
    matching/guided_epipolar_matcher.cc
    matching/in_memory_features_and_matches_database.cc
    matching/rocksdb_features_and_matches_database.cc)
  target_include_directories(theia_matching PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_matching ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_matching)

if(BUILD_LIB_theia_math)
  add_library(theia_math
    math/closed_form_polynomial_solver.cc
    math/constrained_l1_solver.cc
    math/find_polynomial_roots_companion_matrix.cc
    math/find_polynomial_roots_jenkins_traub.cc
    math/matrix/sparse_cholesky_llt.cc
    math/matrix/sparse_matrix.cc
    math/polynomial.cc
    math/probability/sequential_probability_ratio.cc
    math/qp_solver.cc
    math/rotation.cc)
  target_include_directories(theia_math PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_math ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_math)

if(BUILD_LIB_theia_sfm)
  add_library(theia_sfm
    sfm/bundle_adjustment/bundle_adjust_two_views.cc
    sfm/bundle_adjustment/bundle_adjuster.cc
    sfm/bundle_adjustment/bundle_adjustment.cc
    sfm/bundle_adjustment/create_loss_function.cc
    sfm/bundle_adjustment/optimize_relative_position_with_known_rotation.cc
    sfm/camera/camera_intrinsics_model.cc
    sfm/camera/camera.cc
    sfm/camera/division_undistortion_camera_model.cc
    sfm/camera/fisheye_camera_model.cc
    sfm/camera/fov_camera_model.cc
    sfm/camera/pinhole_camera_model.cc
    sfm/camera/pinhole_radial_tangential_camera_model.cc
    sfm/camera/projection_matrix_utils.cc
    # sfm/colorize_reconstruction.cc
    sfm/estimate_track.cc
    sfm/estimate_twoview_info.cc
    sfm/estimators/estimate_absolute_pose_with_known_orientation.cc
    sfm/estimators/estimate_calibrated_absolute_pose.cc
    sfm/estimators/estimate_dominant_plane_from_points.cc
    sfm/estimators/estimate_essential_matrix.cc
    sfm/estimators/estimate_fundamental_matrix.cc
    sfm/estimators/estimate_homography.cc
    sfm/estimators/estimate_relative_pose_with_known_orientation.cc
    sfm/estimators/estimate_relative_pose.cc
    sfm/estimators/estimate_similarity_transformation_2d_3d.cc
    sfm/estimators/estimate_triangulation.cc
    sfm/estimators/estimate_uncalibrated_absolute_pose.cc
    sfm/estimators/estimate_uncalibrated_relative_pose.cc
    # sfm/exif_reader.cc
    sfm/extract_maximally_parallel_rigid_subgraph.cc
    # sfm/feature_extractor_and_matcher.cc
    # sfm/feature_extractor.cc
    sfm/filter_view_graph_cycles_by_rotation.cc
    sfm/filter_view_pairs_from_orientation.cc
    sfm/filter_view_pairs_from_relative_translation.cc
    sfm/find_common_tracks_in_views.cc
    sfm/find_common_views_by_name.cc
    sfm/global_pose_estimation/compute_triplet_baseline_ratios.cc
    sfm/global_pose_estimation/least_unsquared_deviation_position_estimator.cc
    sfm/global_pose_estimation/linear_position_estimator.cc
    sfm/global_pose_estimation/linear_rotation_estimator.cc
    sfm/global_pose_estimation/nonlinear_position_estimator.cc
    sfm/global_pose_estimation/nonlinear_rotation_estimator.cc
    sfm/global_pose_estimation/pairwise_rotation_error.cc
    sfm/global_pose_estimation/pairwise_translation_and_scale_error.cc
    sfm/global_pose_estimation/pairwise_translation_error.cc
    sfm/global_pose_estimation/robust_rotation_estimator.cc
    sfm/global_reconstruction_estimator.cc
    sfm/gps_converter.cc
    sfm/hybrid_reconstruction_estimator.cc
    sfm/incremental_reconstruction_estimator.cc
    sfm/localize_view_to_reconstruction.cc
    sfm/pose/dls_impl.cc
    sfm/pose/dls_pnp.cc
    sfm/pose/eight_point_fundamental_matrix.cc
    sfm/pose/essential_matrix_utils.cc
    sfm/pose/five_point_focal_length_radial_distortion.cc
    sfm/pose/five_point_relative_pose.cc
    sfm/pose/four_point_focal_length_helper.cc
    sfm/pose/four_point_focal_length.cc
    sfm/pose/four_point_homography.cc
    sfm/pose/four_point_relative_pose_partial_rotation.cc
    sfm/pose/fundamental_matrix_util.cc
    sfm/pose/perspective_three_point.cc
    sfm/pose/position_from_two_rays.cc
    sfm/pose/relative_pose_from_two_points_with_known_rotation.cc
    sfm/pose/seven_point_fundamental_matrix.cc
    sfm/pose/sim_transform_partial_rotation.cc
    sfm/pose/test_util.cc
    sfm/pose/three_point_relative_pose_partial_rotation.cc
    sfm/pose/two_point_pose_partial_rotation.cc
    sfm/pose/util.cc
    # sfm/reconstruction_builder.cc
    sfm/reconstruction_estimator_utils.cc
    sfm/reconstruction_estimator.cc
    sfm/reconstruction.cc
    sfm/select_good_tracks_for_bundle_adjustment.cc
    sfm/set_camera_intrinsics_from_priors.cc
    sfm/set_outlier_tracks_to_unestimated.cc
    sfm/track_builder.cc
    sfm/track.cc
    sfm/transformation/align_point_clouds.cc
    sfm/transformation/align_reconstructions.cc
    sfm/transformation/align_rotations.cc
    sfm/transformation/gdls_similarity_transform.cc
    sfm/transformation/transform_reconstruction.cc
    sfm/triangulation/triangulation.cc
    sfm/two_view_match_geometric_verification.cc
    sfm/twoview_info.cc
    # sfm/undistort_image.cc
    sfm/view_graph/orientations_from_maximum_spanning_tree.cc
    sfm/view_graph/remove_disconnected_view_pairs.cc
    sfm/view_graph/view_graph.cc
    sfm/view.cc
    sfm/visibility_pyramid.cc)
  target_include_directories(theia_sfm PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_sfm ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_sfm)

if(BUILD_LIB_theia_solvers)
  add_library(theia_solvers
    solvers/exhaustive_sampler.cc
    solvers/prosac_sampler.cc
    solvers/random_sampler.cc)
  target_include_directories(theia_solvers PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_solvers ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_solvers)

if(BUILD_LIB_theia_util)
  add_library(theia_util
    util/filesystem.cc
    util/random.cc
    util/stringprintf.cc
    util/threadpool.cc
    util/timer.cc)
  target_include_directories(theia_util PRIVATE ${INCLUDE_DIRECTORIES})
  install(TARGETS theia_util ARCHIVE DESTINATION lib LIBRARY DESTINATION lib)
endif(BUILD_LIB_theia_util)

install(DIRECTORY . DESTINATION include FILES_MATCHING PATTERN "*.h*")
