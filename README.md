import java.io.*;
import java.util.*;

public class NotesApp {
    
    // Directory to store all note files
    private static final String NOTES_DIRECTORY = "notes/";
    private static final String FILE_EXTENSION = ".txt";
    private static Scanner scanner = new Scanner(System.in);
    
    public static void main(String[] args) {
        // Create notes directory if it doesn't exist
        createNotesDirectory();
        
        System.out.println("=== Welcome to Java Notes Manager ===");
        System.out.println("A simple file I/O demonstration app\n");
        
        boolean running = true;
        while (running) {
            displayMenu();
            int choice = getChoice();
            
            switch (choice) {
                case 1:
                    createNote();
                    break;
                case 2:
                    readNote();
                    break;
                case 3:
                    listAllNotes();
                    break;
                case 4:
                    deleteNote();
                    break;
                case 5:
                    searchNotes();
                    break;
                case 6:
                    running = false;
                    System.out.println("Thank you for using Notes Manager!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
            System.out.println(); // Add spacing between operations
        }
        scanner.close();
    }
    
    /**
     * Creates the notes directory if it doesn't exist
     * Demonstrates basic File operations
     */
    private static void createNotesDirectory() {
        File directory = new File(NOTES_DIRECTORY);
        if (!directory.exists()) {
            boolean created = directory.mkdir();
            if (created) {
                System.out.println("Created notes directory: " + NOTES_DIRECTORY);
            }
        }
    }
    
    /**
     * Displays the main menu options
     */
    private static void displayMenu() {
        System.out.println("=== Notes Manager Menu ===");
        System.out.println("1. Create New Note");
        System.out.println("2. Read Note");
        System.out.println("3. List All Notes");
        System.out.println("4. Delete Note");
        System.out.println("5. Search Notes");
        System.out.println("6. Exit");
        System.out.print("Enter your choice (1-6): ");
    }
    
    /**
     * Gets user's menu choice with basic input validation
     */
    private static int getChoice() {
        try {
            return Integer.parseInt(scanner.nextLine());
        } catch (NumberFormatException e) {
            return -1; // Invalid choice
        }
    }
    
    /**
     * Creates a new note and saves it to a file using FileWriter
     * Demonstrates writing to files with proper exception handling
     */
    private static void createNote() {
        System.out.println("\n=== Create New Note ===");
        System.out.print("Enter note title: ");
        String title = scanner.nextLine().trim();
        
        if (title.isEmpty()) {
            System.out.println("Title cannot be empty!");
            return;
        }
        
        // Clean title for filename (remove special characters)
        String filename = title.replaceAll("[^a-zA-Z0-9\\s]", "").replaceAll("\\s+", "_");
        String filepath = NOTES_DIRECTORY + filename + FILE_EXTENSION;
        
        System.out.println("Enter your note content (type 'END' on a new line to finish):");
        StringBuilder content = new StringBuilder();
        String line;
        
        // Collect multi-line input
        while (!(line = scanner.nextLine()).equals("END")) {
            content.append(line).append("\n");
        }
        
        // Write note to file using FileWriter
        try (FileWriter writer = new FileWriter(filepath)) {
            // Write title and creation timestamp
            writer.write("Title: " + title + "\n");
            writer.write("Created: " + new Date() + "\n");
            writer.write("=" + "=".repeat(50) + "\n");
            writer.write(content.toString());
            
            System.out.println("✓ Note saved successfully as: " + filename + FILE_EXTENSION);
            
        } catch (IOException e) {
            System.out.println("✗ Error saving note: " + e.getMessage());
        }
    }
    
    /**
     * Reads and displays a note from file using FileReader and BufferedReader
     * Demonstrates reading from files with proper resource management
     */
    private static void readNote() {
        System.out.println("\n=== Read Note ===");
        
        // Show available notes first
        listAvailableNotes();
        
        System.out.print("Enter note filename (without .txt): ");
        String filename = scanner.nextLine().trim();
        
        if (filename.isEmpty()) {
            System.out.println("Filename cannot be empty!");
            return;
        }
        
        String filepath = NOTES_DIRECTORY + filename + FILE_EXTENSION;
        File file = new File(filepath);
        
        if (!file.exists()) {
            System.out.println("✗ Note not found: " + filename + FILE_EXTENSION);
            return;
        }
        
        // Read file using FileReader and BufferedReader
        try (FileReader fileReader = new FileReader(filepath);
             BufferedReader bufferedReader = new BufferedReader(fileReader)) {
            
            System.out.println("\n" + "=".repeat(60));
            String line;
            while ((line = bufferedReader.readLine()) != null) {
                System.out.println(line);
            }
            System.out.println("=".repeat(60));
            
        } catch (IOException e) {
            System.out.println("✗ Error reading note: " + e.getMessage());
        }
    }
    
    /**
     * Lists all available note files
     * Demonstrates directory listing and file filtering
     */
    private static void listAllNotes() {
        System.out.println("\n=== All Notes ===");
        listAvailableNotes();
    }
    
    /**
     * Helper method to list available notes with file information
     */
    private static void listAvailableNotes() {
        File directory = new File(NOTES_DIRECTORY);
        File[] files = directory.listFiles((dir, name) -> name.endsWith(FILE_EXTENSION));
        
        if (files == null || files.length == 0) {
            System.out.println("No notes found. Create your first note!");
            return;
        }
        
        System.out.println("Available notes:");
        for (int i = 0; i < files.length; i++) {
            File file = files[i];
            String name = file.getName().replace(FILE_EXTENSION, "");
            long size = file.length();
            Date modified = new Date(file.lastModified());
            
            System.out.printf("%d. %s (%d bytes, modified: %tc)%n", 
                            i + 1, name, size, modified);
        }
    }
    
    /**
     * Deletes a note file
     * Demonstrates file deletion operations
     */
    private static void deleteNote() {
        System.out.println("\n=== Delete Note ===");
        
        listAvailableNotes();
        
        System.out.print("Enter note filename to delete (without .txt): ");
        String filename = scanner.nextLine().trim();
        
        if (filename.isEmpty()) {
            System.out.println("Filename cannot be empty!");
            return;
        }
        
        String filepath = NOTES_DIRECTORY + filename + FILE_EXTENSION;
        File file = new File(filepath);
        
        if (!file.exists()) {
            System.out.println("✗ Note not found: " + filename + FILE_EXTENSION);
            return;
        }
        
        System.out.print("Are you sure you want to delete '" + filename + "'? (y/N): ");
        String confirmation = scanner.nextLine().trim().toLowerCase();
        
        if (confirmation.equals("y") || confirmation.equals("yes")) {
            if (file.delete()) {
                System.out.println("✓ Note deleted successfully: " + filename + FILE_EXTENSION);
            } else {
                System.out.println("✗ Failed to delete note: " + filename + FILE_EXTENSION);
            }
        } else {
            System.out.println("Delete operation cancelled.");
        }
    }
    
    /**
     * Searches for content across all notes
     * Demonstrates reading multiple files and text searching
     */
    private static void searchNotes() {
        System.out.println("\n=== Search Notes ===");
        System.out.print("Enter search term: ");
        String searchTerm = scanner.nextLine().trim();
        
        if (searchTerm.isEmpty()) {
            System.out.println("Search term cannot be empty!");
            return;
        }
        
        File directory = new File(NOTES_DIRECTORY);
        File[] files = directory.listFiles((dir, name) -> name.endsWith(FILE_EXTENSION));
        
        if (files == null || files.length == 0) {
            System.out.println("No notes to search.");
            return;
        }
        
        System.out.println("Searching for: \"" + searchTerm + "\"");
        System.out.println("-".repeat(40));
        
        boolean found = false;
        String searchLower = searchTerm.toLowerCase();
        
        for (File file : files) {
            try (FileReader fileReader = new FileReader(file);
                 BufferedReader bufferedReader = new BufferedReader(fileReader)) {
                
                String line;
                int lineNumber = 1;
                boolean fileHasMatch = false;
                
                while ((line = bufferedReader.readLine()) != null) {
                    if (line.toLowerCase().contains(searchLower)) {
                        if (!fileHasMatch) {
                            System.out.println("\n📝 " + file.getName().replace(FILE_EXTENSION, ""));
                            fileHasMatch = true;
                            found = true;
                        }
                        System.out.printf("   Line %d: %s%n", lineNumber, line.trim());
                    }
                    lineNumber++;
                }
                
            } catch (IOException e) {
                System.out.println("Error searching file " + file.getName() + ": " + e.getMessage());
            }
        }
        
        if (!found) {
            System.out.println("No matches found for: \"" + searchTerm + "\"");
        }
    }
}
