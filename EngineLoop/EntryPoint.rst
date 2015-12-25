Entry point and main loop
=========================

It is a good start to learn from the entry point and main loop. All related coderesides in Path <Engine Root>/Engine/Source/Runtime/Launch/.

Here you can find all the code for creating application on different platform.

Take a look at LaunchMac.cpp in <Engine Root>/Engine/Source/Runtime/Launch/Private/Mac. This is the implementation on Mac OS.

We can easily tell the entry point is INT32_MAIN_INT32_ARGC_TCHAR_ARGV().

What makes sense is the function GuardedMain which encapsulates the main loop of UE4.

The main loop of UE4 is quite simple and it is something as the code snippet shows below::

    int32 GuardedMain( const TCHAR* CmdLine )
    {
    	// make sure GEngineLoop::Exit() is always called.
    	struct EngineLoopCleanupGuard 
    	{ 
    		~EngineLoopCleanupGuard()
    		{
    			EngineExit();
    		}
    	} CleanupGuard;	
    
        ...
    
    	EnginePreInit( CmdLine );
    
        ...
    
    	EngineInit();
    
        ...
    
        while( !GIsRequestingExit )
        {
            EngineTick();
        }
    
        ...
    }


EnginePreInit
-------------

* Set working directory
* Set commandline
* Initialize GMalloc
* Set GameNam
* Initialize log console
* Initialize std out devices
* Initialize file manager
* Remember thread id of the main thread which is game thread
* Initialize random number generator
* Start Stat thread
* LoadCoreModules
* Initialize rendering CVars Caching
* Allocate Thread Pool
* LoadPreInitModules 
* AppInit
* Initialize system settings
* Preload resolution settings
* Scalability load state
* Platform init
* platform memory init
* Create IO system
* Create platform features module
* Initialize Game Phys
* Clean file cache
* Initialize Text Localization
* Show splash
* Create Slate application
* Create engine font services
* Initialize RHI
* Create shader compiling manager
* create distance field async queue
* Initialize Shader Types
* Load global shaders
* Register UObject classes and initialize properties 
* Init Default Materials
* Initialize texture streaming system
* Load seek-free startup packages
* Setup GC optimizations
* Load startup core modules
* Initialize slate renderer
* Loading modules for loading screen
* Init movie player and play movie
* Platform post init 
* RHIPostInit
* Start rendering thread
* Load startup modules
* Initialize GEngine and parse command line
* Initialize profile visualizers
* Init high res screenshot system

EngineInit
----------

EngineTick
----------

EngineExit
----------






