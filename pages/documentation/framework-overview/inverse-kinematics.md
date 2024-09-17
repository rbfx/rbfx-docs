Inverse Kinematics {#inverse-kinematics}
========================================

Inverse Kinematics (IK) system is used to calculate the positions and rotations of bones in a skeleton to achieve a desired end-effector position.

## IKSolver

The **IKSolver** class is designed to be used in conjunction with other components of the engine to animate characters and objects in a realistic manner.

Contains methods such as Solve() which performs the IK calculations.

The **IKSolver** searches for **IKSolverComponent** components within the same node and it's children and processes each one every frame. The IKSolverComponent serves as a base class for components that specify the bone chains to be solved.

## IKArmSolver

## IKChainSolver

## IKHeadSolver

## IKIdentitySolver

## IKLegSolver

## IKLimbSolver

## IKRotateTo

## IKSpineSolver

## IKStickTargets

To be continued... Please contribute to documentation at [rbfx-docs](https://github.com/rbfx/rbfx-docs).