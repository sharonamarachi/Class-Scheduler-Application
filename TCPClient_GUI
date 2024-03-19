
import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.geometry.Insets;
import javafx.geometry.HPos;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.scene.text.Font;
import javafx.scene.text.FontWeight;
import javafx.stage.Stage;
import javafx.stage.Window;

import java.io.*;
import java.net.InetAddress;
import java.net.Socket;
import java.net.UnknownHostException;
import java.time.LocalDate;
import java.util.HashMap;

import static javafx.geometry.HPos.CENTER;

public class Main extends Application {
    static InetAddress host;
    static final int PORT = 123;
    static HashMap<String, ClassDetails> scheduledClasses = new HashMap<>();

    @Override
    public void start(Stage primaryStage) {
        try {

            GridPane gridPane = generalGridFormat();

            // Title and Format
            Label mainTitle = new Label("Class Scheduler");
            mainTitle.setFont(Font.font("Arial", FontWeight.BOLD, 24));
            gridPane.add(mainTitle, 0,0);
            GridPane.setHalignment(mainTitle, CENTER);

            // Create buttons
            Button addClassButton = new Button("Add Class");
            Button viewButton = new Button("View/Remove Classes");
            Button displayButton = new Button("Display Schedule");
            Button termnateButton = new Button("TERMINATE");
            termnateButton.setStyle("-fx-background-color:rgba(236,115,115,0.83);");
            Label mainLabel = new Label("Response From Server Will Display Here");

            // Action to switch to ClassApplication scene
            addClassButton.setOnAction(new EventHandler<ActionEvent>() {
                @Override
                public void handle(ActionEvent event) {
                    ClassApplication classApplication = new ClassApplication(scheduledClasses);
                    Scene classScene = classApplication.createScene(primaryStage);
                    primaryStage.setScene(classScene);
                }
            });

            // Action to switch to scene to view and remove Classes
            viewButton.setOnAction(new EventHandler<ActionEvent>() {
                @Override
                public void handle(ActionEvent event) {
                    VRClass vrClass = new VRClass(scheduledClasses);
                    Scene vrScene = vrClass.createScene(primaryStage);
                    primaryStage.setScene(vrScene);

                }
            });

            // Action to display schedule
            displayButton.setOnAction(new EventHandler<ActionEvent>() {
                @Override
                public void handle(ActionEvent event) {
                    try
                    {
                        host = InetAddress.getLocalHost();
                    }
                    catch(UnknownHostException a)
                    {
                        System.out.println("Host ID not found!");
                        System.exit(1);
                    }
                    Socket link = null;
                    try
                    {
                        link = new Socket(host,PORT);
                        //link = new Socket( "192.168.0.59", PORT);
                        BufferedReader in = new BufferedReader(new InputStreamReader(link.getInputStream()));
                        PrintWriter out = new PrintWriter(link.getOutputStream(),true);

                        String message = "";
                        String response= "";
                        message = "DISPLAY";
                        out.println(message);
                        response = in.readLine();
                        mainLabel.setText(response);
                        showAlert(Alert.AlertType.INFORMATION, gridPane.getScene().getWindow(), "Response from server: ", response);


                    }
                    catch(IOException a)
                    {
                        a.printStackTrace();
                    }
                    finally
                    {
                        try
                        {
                            System.out.println("\n* Closing connection... *");
                            link.close();
                        }catch(IOException a)
                        {
                            System.out.println("Unable to disconnect/close!");
                            System.exit(1);
                        }
                    }

                }
            });

            termnateButton.setOnAction(new EventHandler<ActionEvent>() {
                @Override
                public void handle(ActionEvent event) {

                    try
                    {
                        host = InetAddress.getLocalHost();
                    }
                    catch(UnknownHostException e)
                    {
                        System.out.println("Host ID not found!");
                        System.exit(1);
                    }
                    Socket link = null;
                    try {

                        link = new Socket(host,PORT);

                        BufferedReader in = new BufferedReader(new InputStreamReader(link.getInputStream()));
                        PrintWriter out = new PrintWriter(link.getOutputStream(),true);
                        String message = "";
                        String response= null;

                        message = "TERMINATE";
                        out.println(message);
                        response = in.readLine();
                        System.out.println("Server response: " + response);
                        if (response.equals(" Server terminated")) {
                            link.close();
                        }
                    }catch(IOException e){
                        System.out.println("Unable to disconnect/close!");
                        System.exit(1);
                    }
                }
            });

            // Adding buttons to the Grid Pane
            gridPane.add(addClassButton, 0, 1);
            gridPane.add(viewButton, 0, 3);
            gridPane.add(displayButton, 0, 5);
            gridPane.add(termnateButton, 0, 7);
            gridPane.add(mainLabel, 0,9);

            // Set the scene to the stage
            Scene scene = new Scene(gridPane);
            primaryStage.setScene(scene);
            primaryStage.show();

        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    //Format for grid pane
    public GridPane generalGridFormat() {
        GridPane mainGridPane = new GridPane();
        mainGridPane.setMinSize(500,300);
        mainGridPane.setPadding(new javafx.geometry.Insets(10,10,10,10));
        mainGridPane.setVgap(5);
        mainGridPane.setHgap(5);
        mainGridPane.setAlignment(javafx.geometry.Pos.CENTER);
        mainGridPane.setStyle("-fx-background-color:WHITE;");
        return mainGridPane;
    }

    void saveToCSV(HashMap<String, ClassDetails> scheduledClasses) {
        try (PrintWriter writer = new PrintWriter(new File("classes.csv"))) {
            StringBuilder sb = new StringBuilder();
            sb.append("Class Name,Start Date,End Date,Time,Location\n");
            for (ClassDetails classDetails : scheduledClasses.values()) {
                sb.append(classDetails.getClassName()).append(",")
                        .append(classDetails.getStartDate()).append(",")
                        .append(classDetails.getEndDate()).append(",")
                        .append(classDetails.getTime()).append(",")
                        .append(classDetails.getLocation()).append("\n");
            }
            writer.write(sb.toString());
            System.out.println("Update in the CSV file!");
        } catch (FileNotFoundException e) {
            System.err.println("There is not file to update");
        }
    }

    HashMap<String, ClassDetails> loadFromCSV() {
        HashMap<String, ClassDetails> scheduledClasses = new HashMap<>();
        try (BufferedReader br = new BufferedReader(new FileReader("classes.csv"))) {
            String line;
            br.readLine(); // Skip header line
            while ((line = br.readLine()) != null) {
                String[] data = line.split(",");
                if (data.length == 5) {
                    String className = data[0];
                    String startDate = data[1];
                    String endDate = data[2];
                    String time = data[3];
                    String location = data[4];
                    scheduledClasses.put(className, new ClassDetails(className, startDate, endDate, time, location));
                }
            }
            System.out.println("Loading Classes");
        } catch (FileNotFoundException e) {
            System.err.println("CSV file not found");
        } catch (IOException e) {
            System.err.println("Error!");
        }
        return scheduledClasses;
    }
    //Alert window
    public void showAlert(Alert.AlertType alertType, Window owner, String title, String message) {
        Alert alert = new Alert(alertType);
        alert.setTitle(title);
        alert.setHeaderText(null); //no header
        alert.setContentText(message);
        alert.initOwner(owner);
        alert.show();
    }




    public static void main(String[] args) {
        launch(args);
    }
}

class ClassApplication extends Main {
    static InetAddress host;
    static final int PORT = 123;
    private HashMap<String, ClassDetails> scheduledClasses;
    private TextField classNameField;
    private DatePicker dateStartField;
    private DatePicker dateEndField;
    private ComboBox<String> timeslots;
    private TextField locationField;
    private Stage primaryStage;

    public ClassApplication(HashMap<String, ClassDetails> scheduledClasses) {
        this.scheduledClasses = loadFromCSV();
    }

    public void addUIControls(GridPane gridPane) {

        //Header and format
        Label headerLabel = new Label("New Class Form");
        headerLabel.setFont(Font.font("Arial", FontWeight.BOLD, 20));
        gridPane.add(headerLabel, 0,0);
        GridPane.setHalignment(headerLabel, HPos.LEFT);
        GridPane.setMargin(headerLabel, new Insets(20, 0,20,0));

        //Class Name
        Label labelCN = new Label("Class Name");
        gridPane.add(labelCN,0,1);
        classNameField = new TextField();
        gridPane.add(classNameField,1,1);

        //Start Date of Class
        Label labelDateStart = new Label("Starting Date: ");
        gridPane.add(labelDateStart,0,2);
        dateStartField = new DatePicker();
        gridPane.add(dateStartField,1,2);

        //End Date of Class
        Label labelDateEnd = new Label("Ending Date: ");
        gridPane.add(labelDateEnd,0,3);
        dateEndField = new DatePicker();
        gridPane.add(dateEndField,1,3);

        //Time of Class
        Label labelTime = new Label("Time: ");
        gridPane.add(labelTime,0,4);
        timeslots = new ComboBox<>();
        timeslots.getItems().addAll(
                "9:00-10:00", "10:00-11:00",
                "11:00-12:00", "12:00-13:00",
                "13:00-14:00", "14:00-15:00",
                "15:00-16:00", "16:00-17:00",
                "18:00-19:00", "18:00-19:00",
                "19:00-20:00"
        );
        gridPane.add(timeslots,1,4);

        //Location of class
        Label labelLocation = new Label("Room");
        gridPane.add(labelLocation,0,5);
        locationField = new TextField();
        gridPane.add(locationField,1,5);

        //Back button
        Button backButton = new Button("Back");
        gridPane.add(backButton, 0,7);
        GridPane.setHalignment(backButton, HPos.LEFT);
        GridPane.setMargin(backButton, new Insets(20, 0,20,0));
        //When its clicked it goes back to homepage
        backButton.setOnAction(e -> {
            Main main = new Main();
            main.start((Stage) gridPane.getScene().getWindow());
        });

        //Summit button
        Button submitButton = new Button("Submit");
        submitButton.setPrefHeight(20);
        submitButton.setPrefWidth(100);
        gridPane.add(submitButton, 1,7);
        GridPane.setHalignment(submitButton, HPos.RIGHT);
        GridPane.setMargin(submitButton, new Insets(20, 0,20,0));

        //Sever Label
        Label classAppLabel = new Label("Response From Server Will Display Here");
        gridPane.add(classAppLabel, 0,8);
        GridPane.setMargin(classAppLabel, new Insets(20, 0,20,0));

        //Action when submit is clicked
        submitButton.setOnAction(new EventHandler<ActionEvent>() {
            @Override
            public void handle(ActionEvent event) {
                Socket link = null;
                try
                {
                    host = InetAddress.getLocalHost();
                }
                catch(UnknownHostException e)
                {
                    System.out.println("Host ID not found!");
                    System.exit(1);
                }
                try {
                    String className = classNameField.getText();
                    LocalDate startDate = dateStartField.getValue();
                    LocalDate endDate = dateEndField.getValue();
                    String selectedTime = (String) timeslots.getValue();
                    String selectedLocation = locationField.getText();

                    //Checks if the class name field is empty
                    if(className.isEmpty()) {
                        showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "Please enter class name");
                        return;
                    }

                    //Checks if user did not select a date and if the start date is before end date
                    if (startDate == null || endDate == null || startDate.isAfter(endDate)) {
                        showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "Please select valid start and end dates");
                        return;
                    }

                    //Checks if there was a selected timeslot
                    if(selectedTime == null) {
                        showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "Please select a timeslot");
                        return;
                    }

                    //Checks if the location field is empty
                    if(selectedLocation.isEmpty()) {
                        showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "Please enter class location");
                        return;
                    }


                } catch (Exception e) {
                    e.printStackTrace();
                    showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "An unexpected error occurred");
                }
                try{
                    String className = classNameField.getText();
                    LocalDate startDate = dateStartField.getValue();
                    LocalDate endDate = dateEndField.getValue();
                    String selectedTime = (String) timeslots.getValue();
                    String selectedLocation = locationField.getText();
                    //connecting with server
                    link = new Socket(host,PORT);
                    BufferedReader in = new BufferedReader(new InputStreamReader(link.getInputStream()));
                    PrintWriter out = new PrintWriter(link.getOutputStream(),true);
                    String message = "";
                    String response= null;

                    message = "A," +  className+ "," + startDate.toString()+ ","+ endDate.toString()+"," + selectedTime+ "," + selectedLocation;
                    out.println(message);
                    response = in.readLine();
                    if (response.startsWith("ERROR:")) {
                        String errorMessage = response.substring(6); // Extract the error message
                        // Show an alert dialog with the error message
                        showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!",  errorMessage);
                    } else {
                        // Add the new class to the scheduledClasses HashMap
                        ClassDetails newClass = new ClassDetails(className, startDate.toString(), endDate.toString(), selectedTime, selectedLocation);
                        scheduledClasses.put(className, newClass);
                        //add new class in the CSV
                        saveToCSV(scheduledClasses);
                        // Update the list view in the VRClass scene
                        VRClass vrClass = new VRClass(scheduledClasses);
                        Scene vrScene = vrClass.createScene(primaryStage); // Pass primaryStage reference
                        //Send an alert for confirmation
                        showAlert(Alert.AlertType.CONFIRMATION, gridPane.getScene().getWindow(), "Perfect!",  classNameField.getText() + " was created successfully");

                        //Clear fields
                        classNameField.clear();
                        dateStartField.setValue(null);
                        dateEndField.setValue(null);
                        timeslots.getSelectionModel().clearSelection();
                        locationField.clear();
                    }
                    classAppLabel.setText(response);
                }catch (IOException e) {
                    e.printStackTrace();
                    showAlert(Alert.AlertType.ERROR, gridPane.getScene().getWindow(), "Error!", "An unexpected error occurred while sending data");
                }
            }

        });
    }


    public Scene createScene(Stage primaryStage) {
        this.primaryStage = primaryStage;
        GridPane gridPane = generalGridFormat();
        addUIControls(gridPane);
        return new Scene(gridPane);
    }



}

class VRClass extends Main {
    static InetAddress host;
    static final int PORT = 123;
    private HashMap<String, ClassDetails> scheduledClasses;
    private Stage primaryStage;
    ListView<String> classListView;


    public VRClass(HashMap<String, ClassDetails> scheduledClasses) {

        this.scheduledClasses = loadFromCSV();
    }

    private GridPane listViewPane() {
        GridPane secondgridPane = new GridPane();
        secondgridPane.setMinSize(500, 400);
        secondgridPane.setPadding(new javafx.geometry.Insets(10, 10, 10, 10));
        secondgridPane.setVgap(5);
        secondgridPane.setHgap(5);
        secondgridPane.setAlignment(javafx.geometry.Pos.CENTER);

        Label listOfClasses = new Label("List of Classes");
        listOfClasses.setFont(Font.font("Arial", FontWeight.BOLD, 24));
        secondgridPane.add(listOfClasses, 0, 0);
        GridPane.setHalignment(listOfClasses, CENTER);
        GridPane.setMargin(listOfClasses, new javafx.geometry.Insets(20, 0, 20, 0));

        //Creating Listview
        classListView = new ListView<>();
        classListView.getItems().addAll(scheduledClasses.keySet());
        secondgridPane.add(classListView, 0, 1);

        // Back button
        Button backButton = new Button("Back");
        backButton.setPrefHeight(20);
        backButton.setPrefWidth(70);
        secondgridPane.add(backButton, 0, 2);
        GridPane.setHalignment(backButton, javafx.geometry.HPos.LEFT);
        GridPane.setMargin(backButton, new javafx.geometry.Insets(20, 0, 10, 0));
        backButton.setOnAction(event -> {
            Main main = new Main();
            main.start((Stage) secondgridPane.getScene().getWindow());
        });

        Button deleteButton = new Button("Delete");
        deleteButton.setPrefHeight(20);
        deleteButton.setPrefWidth(70);
        secondgridPane.add(deleteButton, 0, 2);
        GridPane.setHalignment(deleteButton, HPos.RIGHT);
        GridPane.setMargin(deleteButton, new javafx.geometry.Insets(20, 0, 10, 0));

        Label VRLabel = new Label("Response From Server Will Display Here");
        secondgridPane.add(VRLabel, 0,3);
        GridPane.setMargin(VRLabel, new Insets(20, 0,20,0));


        deleteButton.setOnAction(event -> {
            Socket link = null;
            try
            {
                host = InetAddress.getLocalHost();
            }
            catch(UnknownHostException e)
            {
                System.out.println("Host ID not found!");
                System.exit(1);
            }
            try{
                String selectedClass = classListView.getSelectionModel().getSelectedItem();
                if (selectedClass != null) {
                    System.out.println("Deleted Class: " + selectedClass); // prints out the selected class that is deleted
                    scheduledClasses.remove(selectedClass);
                    classListView.getItems().remove(selectedClass);
                    saveToCSV(scheduledClasses); // Update CSV after deletion
                    link = new Socket(host,PORT);
                    BufferedReader in = new BufferedReader(new InputStreamReader(link.getInputStream()));
                    PrintWriter out = new PrintWriter(link.getOutputStream(),true);
                    String message = "";
                    String response= null;

                    //System.out.println("Enter message to be sent to server: ");
                    message = "R," + selectedClass;
                    out.println(message);
                    response = in.readLine();
                    VRLabel.setText(response);
                }
            }catch(Exception e){
                e.printStackTrace();
            }
        });

        return secondgridPane;
    }


    public Scene createScene(Stage primaryStage) {
        this.primaryStage = primaryStage;
        GridPane gridPane = listViewPane();
        return new Scene(gridPane);
    }

}

class ClassDetails {
    private String className;
    private String startDate;
    private String endDate;
    private String time;
    private String location;

    public ClassDetails(String className, String startDate, String endDate, String time, String location) {
        this.className = className;
        this.startDate = startDate;
        this.endDate = endDate;
        this.time = time;
        this.location = location;
    }

    public String getClassName() {
        return className;
    }

    public String getStartDate() {
        return startDate;
    }

    public String getEndDate() {
        return endDate;
    }

    public String getTime() {
        return time;
    }

    public String getLocation() {
        return location;
    }
}


