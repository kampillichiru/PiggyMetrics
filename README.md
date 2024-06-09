private static Path resolveSecurePath(Path basePath, String fileName) throws IOException {
		try {
			Path basePath = Paths.get(fileName);
			// Ensure the directory exists
			if (!Files.exists(basePath)) {
				throw new IOException("Directory does not exist: " + stagingFilePath);
			}

			// Ensure it is a directory
			if (!Files.isDirectory(basePath)) {
				throw new IOException("The path is not a directory: " + stagingFilePath);
			}

			// Ensure the application has necessary permissions (read/write)
			if (!Files.isReadable(basePath) || !Files.isWritable(basePath)) {
				throw new IOException("Insufficient permissions for the staging directory: " + stagingFilePath);
			}
			// Normalize the base path and user input
			Path resolvedPath = basePath.resolve(fileName).normalize();

			// Ensure the resolved path starts with the intended base directory
			if (!resolvedPath.startsWith(basePath)) {
				throw new IOException("Potential path traversal detected");
			}

			// Ensure the file exists if that's a requirement
			if (!Files.exists(resolvedPath)) {
				throw new IOException("File does not exist");
			}
		} catch (InvalidPathException e) {
			throw new IOException("Invalid file path", e);
		}

		return resolvedPath;
	}
