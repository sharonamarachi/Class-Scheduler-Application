import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;
import java.time.LocalDate;
import java.util.Arrays;
import java.util.HashMap;

public class TCPEchoServer {
    private static ServerSocket servSock;
    private static final int PORT = 123;
    private static int clientConnections = 0;
    private static HashMap<String,String> schedule = new HashMap<>(); // Data structure to store classes


    public static void main(String[] args) {
        System.out.println("Opening port...\n");
        try
        {
            servSock = new ServerSocket(PORT);
            int x = countLines(); // Count number of lines in file
            if( x > 0){ // Load class schedule from file if it exists
                loadHashMap();
                //loadGUIhashmap();
            }
            System.out.println(schedule.size() + " classes loaded from file");
        }
        catch(IOException e)
        {
            System.out.println("Unable to attach to port!");
            System.exit(1);
        }
        run();   // Start server
    }
    private static void run(){
        String[] classInfo = new String[6]; // Array to store message from the client side when it's split
        String newMessage = "";  // String to store new message for client
        boolean connection = true; // boolean to control the while statement
        Socket link = null;//Step 2.
        try {
            while (connection) {
                // Accept client connection
                link = servSock.accept();
                clientConnections++;
                // Input and output streams for communication
                BufferedReader in = new BufferedReader(new InputStreamReader(link.getInputStream()));
                PrintWriter out = new PrintWriter(link.getOutputStream(), true);
                String message = in.readLine(); // Read message from client

                //Terminate Button
                if (message.equals("TERMINATE"))  {
                    // Check message type and perform corresponding action
                    newMessage = "Server is shutting down";
                    out.println("Echo Message: " + newMessage); // Send message to client
                    System.out.println("Message received from client: " + clientConnections + "  " + message);
                    connection = false;

                }

                if (message.equals("Send CSV data"))  { // Check message type and perform corresponding action
                    out.println(loadGUIhashmap(in)); // Send message to client
                    System.out.println(loadGUIhashmap(in));
                }
                else {
                    //Display button
                    if (message.trim().equalsIgnoreCase("DISPLAY")) { // Check message type and perform corresponding action
                        try {
                            if (schedule.isEmpty()) {
                                newMessage = "There are no classes to display";
                                throw new ProcessErrorException("There are no classes to display");
                            } else {
                                for (String classes : schedule.keySet()) {
                                    newMessage = "Classes are displayed in console";
                                    System.out.println("Class Name: " + classes + "   Class Details: " + schedule.get(classes));
                                }
                            }
                        } catch (ProcessErrorException e) {
                            System.out.println(e.getProcessErrorExceptionMessage()); // Custom exception message
                        } catch (Exception e) {
                            System.out.println("An unexpected error occurred while displaying the schedule: ");
                        }
                    } else {
                        System.out.println(message);
                        classInfo = message.split(",");
                        for (int i = 0; i < classInfo.length; i++) {
                            classInfo[i] = classInfo[i].trim().toUpperCase(); // Convert all elements to uppercase
                        }
                        String action = classInfo[0];


                        // Adding class
                        try {
                            if (action.equalsIgnoreCase("A")) {
                                String className = classInfo[1].toUpperCase();
                                String startDate = classInfo[2];
                                String endDate = classInfo[3];
                                String time = classInfo[4];
                                String room = classInfo[5];
                                try {
                                    if (schedule.containsKey(className)) { // Check if class already exists
                                        newMessage = "ERROR: There is a clash, the class could not be added ";
                                        throw new ProcessErrorException("ERROR: There is a clash, the class could not be added ");
                                    }else if ( className.isEmpty() || startDate.isEmpty() || endDate.isEmpty() || time.isEmpty() || room.isEmpty()){
                                        throw new IncorrectActionException("Incomplete");
                                    } else if (schedule.size() == 5 || countLines() == 5) { // Check if schedule is full
                                        newMessage = "ERROR: All slots are taken, you may have to create space";
                                        throw new ProcessErrorException("ERROR: All slots are taken, you may have to create space");
                                    } else {
                                        // Check if the time for the same room is already taken from start date till end date
                                        LocalDate currentDate = LocalDate.parse(startDate);
                                        while (!currentDate.isAfter(LocalDate.parse(endDate))) {
                                            String temp = currentDate.toString() + " - " + time;
                                            if (schedule.containsKey(temp) && schedule.get(temp).split(",")[6].equals(room)) {
                                                newMessage = "ERROR: This time slot for the same room is already taken on " + currentDate.toString();
                                                throw new ProcessErrorException("ERROR: This time slot for the same room is already taken on " + currentDate.toString());
                                            }
                                            currentDate = currentDate.plusDays(7);
                                        }
                                        // Add class to schedule
                                        schedule.put(className, (startDate + "," + endDate + "," + time + "," + room)); // Add to hashmap
                                        writeToFile(className + "," + startDate + "," + endDate + "," + time + "," + room); // Write to file
                                        newMessage = "Class successfully added ";
                                    }
                                }catch (ProcessErrorException e) {
                                    System.out.println(e.getProcessErrorExceptionMessage()); // Custom exception message
                                }
                                catch (NullPointerException e) {
                                    System.out.println("An unexpected error occurred while adding the class: " + e.getMessage());
                                }
                            }
                            ////////////////////////////////////////////////
                            else if (action.equalsIgnoreCase("R")) { // Check message type and perform corresponding action
                                try {
                                    String className = classInfo[1];
                                    if (schedule.containsKey(className)) { // Check if class exists
                                        schedule.remove(className);
                                        RemoveFromFile(className);
                                        newMessage = "The class has been removed successfully";
                                    } else {
                                        newMessage = "The class cannot be removed as it does not exist";
                                        throw new ProcessErrorException("The class cannot be removed as it does not exist");
                                    }
                                } catch (ProcessErrorException e) {
                                    System.out.println(e.getProcessErrorExceptionMessage()); // Custom exception message
                                } catch (NullPointerException e) {
                                    System.out.println("An unexpected error occurred while removing the class: " + e.getMessage());
                                }
                            } else {
                                throw new IncorrectActionException("Invalid action provided: " + action);
                            }

                        } catch (IllegalArgumentException e) {
                            System.out.println("Illegal character entered, process failure");
                        }catch (IncorrectActionException e) {
                            System.out.println("Process failure " + e.getMessage());
                        }
                    }
                    System.out.println("Message received from client: " + clientConnections + "  " + message); // Print message from client
                    out.println(newMessage); // Send message to client
                    Arrays.fill(classInfo, null); // Clear the array
                }
            }
        }
        catch(IOException e)
        {
            e.printStackTrace();
        }
        finally {
            {
                try {
                    System.out.println("\n* Closing connection... *");
                    link.close();
                }
                catch(IOException e)
                {
                    System.out.println("Unable to disconnect!");
                    System.exit(1);
                }
            }
        }
    }
    // Custom exception class for process errors
    public static class ProcessErrorException extends Exception {
        String msg = "There has been a process error";
        public ProcessErrorException(String msg) {
            super(msg);
            this.msg = "Error: " + msg;
        }
        public String getProcessErrorExceptionMessage() {
            return this.msg;
        }
    }


    // Method to store information in a file
    public static void writeToFile(String message) {
        System.out.println("Class added to file");
        try (FileWriter classesFile = new FileWriter("Classes.txt", true)) { // Append to file
            classesFile.write(message); // Write to file
            classesFile.write(System.lineSeparator()); // Add new line
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    // Method to remove information from file
    public static void RemoveFromFile(String keyword) {
        System.out.println("Class removed from file");
        String line;
        StringBuilder updatedFileContent = new StringBuilder(); // String builder to store updated file content
        try (BufferedReader classesFile = new BufferedReader(new FileReader("Classes.txt"))) { // Read file
            boolean foundKeyword = false;
            while ((line = classesFile.readLine()) != null) { // Read file line by line
                if (!foundKeyword && line.contains(keyword)) { // Check if keyword exists
                    foundKeyword = true;
                } else {
                    updatedFileContent.append(line).append(System.lineSeparator()); // Append to string builder
                }
            }
        }catch (IOException e) {
            e.printStackTrace();
        }
        try (FileWriter writer = new FileWriter("Classes.txt")) { // Write to file
            writer.write(updatedFileContent.toString()); // Write to file
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    // Method to count number of lines in a file
    public static int countLines() {
        int lines = 0;
        try (BufferedReader reader = new BufferedReader(new FileReader("Classes.txt"))) { // Read file
            while (reader.readLine() != null) lines++; // Count lines
        } catch (IOException e) {
            e.printStackTrace();
        }
        return lines; // Return number of lines
    }
    // Method to load the hashmap from the file
    public static void loadHashMap() {
        String line;
        try (BufferedReader classesFile = new BufferedReader(new FileReader("Classes.txt"))) { // Read file
            while ((line = classesFile.readLine()) != null) { // Read file line by line
                String[] separate = line.split("[,\\s]+"); // Split line into an array
                if (separate.length >= 5) { // Check if the array has at least 5 elements
                    schedule.put(separate[0],(separate[1] + "," + separate[2] + "," + separate[3] + "," + separate[4] )); // Add to hashmap
                } else {
                    System.out.println(" " + line);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static String loadGUIhashmap(BufferedReader in) throws IOException {
        String request;
        StringBuilder response = new StringBuilder();
        while ((request = in.readLine()) != null) {
            if (request.equals("Send CSV data")) {
                // Send the HashMap data to the client
                for (HashMap.Entry<String, String> data : schedule.entrySet()) {
                    response.append(data.getKey()).append(",").append(data.getValue()).append("\n");
                }
                response.append("Sending Data");
            }
        }
        return response.toString();
    }
    public static class IncorrectActionException extends Exception {
        public IncorrectActionException(String message) {
            super(message);
        }
    }
}
