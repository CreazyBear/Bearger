# ARKit 

ARWorldTrackingConfiguration class tracks the device’s movement with six degrees of freedom (6DOF): specifically, the three rotation axes (roll, pitch, and yaw), and three translation axes (movement in x, y, and z).


* Use  [planeDetection](apple-reference-documentation://hcwVcas8JL)  to find real-world horizontal or vertical surfaces, adding them to the session as  [ARPlaneAnchor](apple-reference-documentation://hckkcZkKgC)  objects. 
* Use  [detectionImages](apple-reference-documentation://hcdL5anlPO)  to recognize and track the movement of known 2D images, adding them to the scene as  [ARImageAnchor](apple-reference-documentation://hcZ46swbSX)  objects.
* Use  [detectionObjects](apple-reference-documentation://hcc8a82t-D)  to recognize known 3D objects, adding them to the scene as  [ARObjectAnchor](apple-reference-documentation://hcJcc8QtSy)  objects. 
* Use hit-testing methods on  [ARFrame](apple-reference-documentation://hcGzMSVvCk) ,  [ARSCNView](apple-reference-documentation://hcfpPXd3Pf) , or  [ARSKView](apple-reference-documentation://hc4i_wCU1N)  to find the 3D positions of real-world features correspoding to a 2D point in the camera view.


[ARKit0-相关实践目录](https://juejin.im/post/5a976cb3f265da4e87010184)


https://github.com/olucurious/Awesome-ARKit