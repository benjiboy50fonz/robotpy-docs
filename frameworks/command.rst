.. _command_framework_docs:

Command Framework
=================

.. note:: The documentation for the new command framework is currently under construction. 
          Please refer to :doc:`WPILib documentation<frc:docs/software/commandbased/index>` for a more complete
          explanation of the framework.

If you're coming from C++ or Java, you are probably familiar with the Command based robot paradigm. All of the pieces you're used to are still available in RobotPy.

This Python-oriented guide will begin by examining the structure of a command-based robot from a Python point of view. Afterwards, we'll look at individual components of the framework. 

Philosophy
----------

The command-based programming style is a style that focuses on what should be done, rather than how something should be done. 
This  framework can seem less dynamic, and more "one and done" than many others, including :class:`MagicRobot <magicbot.magicrobot.MagicRobot>`. Rather than checking something every iteration, for example, one can 
simply instruct the robot to do something at a given moment. It does not necessarily go into detail as to how it must do it. The workings behind
that are pre-defined and structured. 

Command-based programming makes use of the command scheduler to control what happens when. Access to this scheduler allows for us to focus on what we 
want to do rather than how we want to do it. 

**Subsystems** and **Commands** make up the majority of a command-based robot. Let's examine a quick definition of each term:

    * Subsystem: 
        Simply put, this is a software representation of a physical device or mechanism. These should include the low-level functions of each mechanism or motor. Some examples include:
        
            * Arm mechanism
            * Shooter mechanism
            * Drivetrain

    * Command:
        Commands are well-structured, simple state machines that will perform the majority of your tasks. They are constructed to operate autonomously, manually, or both. Commands 
        make up command groups, which are often used for autonomous programs and robot operations that enlarge multiple robot subsystems. Some examples of commands 
        include: 

            * Move forward (autonomous)
            * Drive (manual)
            * Set shooter RPM (both)

        .. note:: Command groups will be discussed more later. If you want to view them immediately, look at :ref:`command_groups_guide`. 

Installation
------------

It's important to note that using the command framework requires an extra RobotPy package. The instructions for the installation of the commands2 
package can be found at the :ref:`install_commands`.

.. _subsystems_guide:


Subsystems
----------

As stated above, subsystems make up the core features of the robot. Interactions with the individual parts of a mechanism should be placed in subsystems.
The primary purpose of subsystems is to operate under a command's request. With that being said, it is important to recognize that some commands will 
attempt to use a subsystem when that subsystem is in use elsewhere. This scenario is called an "interruption", but that will be discussed in the :ref:`commands_guide` guide. 

A simple subsystem for a shooter could look like something below:: 

    from commands2 import SubsystemBase
    
    from ctre import WPI_TalonFX, ControlMode
    
    class Shooter(SubsystemBase):
    
        def __init__(self):
            super().__init__() # Call the constructor of the parent class.
            
            self.shooter_motor = WPI_TalonFX(0) # Define the shooter motor in the subsystem's constructor.
            
        def spinShooter(self):
            """Spin the shooter up to full speed."""

            self.shooter_motor.set(ControlMode.PercentOutput, 1)
        
        def stopShooter(self):
            """Stop the shooter motor."""

            self.shooter_motor.stopMotor()    

        def periodic(self):
            """This loops throughout the robot program."""

            print('Shooter Speed: %s' %(self.shooter_motor.getSelectedSensorVelocity()))

As you can see, we begin the subsystem by declaring a class with a name relating to its' physical function. We inherit the :class:`SubsystemBase <commands2.SubsystemBase>` 
class in order to give this class its' subsystem identity. 

Like many typical classes, we then write a constructor. This constructor will be called as soon as that subsystem is created, which should be 
when the robot is powered on. Therefore, we want to place motor definitions and such in the constructor. Also note that it is important to call
the consturctor of the parent class. Not doing this will not properly establish the subsystem.

.. _commands_guide:

Commands
--------

.. _command_groups_guide:

Command Groups
~~~~~~~~~~~~~~

.. _main_robot_class:

Main Robot Class
----------------

Command Scheduler
-----------------

.. seealso:: :ref:`command_v2_api`

.. seealso:: :ref:`magicbot_framework_docs`
