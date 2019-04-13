# GEngine Superbuild

Project template for creating games with HybridAlpaca's game engine, shortened to 'gengine' due to lack of naming skills.

## Build Instructions

The project comes with no sources by default.  Any local sources you would like to include as part of your project should go into `/source`, either by creating them there or through the use of git submodules.  Third-party libraries and external dependencies should be installed as part of the build system.

Your project's build script resides in `/cmake/projects.cmake`.  This is where you will define all of your local sources, third party libraries, and declare any dependencies between build targets.

### Example

Imagine an example project with the below source tree.

	source/
	├── filesystem/
	├── game-core/
	├── kernel/
	└── renderer/

In our imaginary scenario, `renderer` holds a dependency to `libglfw3`.  If we were to build this project, our `projects.cmake` might look something like the following:

	# Grab vendor libraries from git
	build_from_git (NAME "glfw" REPO "glfw/glfw")

	# Build the kernel
	build_local (NAME "kernel")

	# Build lower-level modules
	build_local (NAME "filesystem" DEPENDS "kernel")
	build_local (NAME "renderer"   DEPENDS "kernel" "glfw")

	# Finally, our game core
	build_local (NAME "game-core" DEPENDS "kernel" "filesystem" "renderer")

GLFW's source tree will be installed to `/build`, and will auto-update with each new release of the git repository.
