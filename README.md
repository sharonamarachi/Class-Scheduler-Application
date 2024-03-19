# Class-Scheduler-Application

Class Scheduler Applicaton is project is a TCP Client-Server Service project which comprises two applications: a GUI-based client and a console-based server, communicating via TCP protocol. Communication alternates between sending and receiving messages, with the client initiating the process. A STOP button terminates the connection, confirmed by the server with a TERMINATE message. The project uses Exception handling, including a custom IncorrectActionException, ensures error management.

For the Class Scheduler Application:
Client: GUI controls facilitate adding, removing, and displaying class schedules. The user inputs class details, triggering corresponding actions like Add Class or Display Schedule. Responses from the server are displayed on the GUI.
Server: Utilizing a memory-based data structure like HashMap, the server manages class schedules. It checks for scheduling clashes when adding a class, removes classes, and displays schedules upon request. Console output shows the schedule details.

Overall, the project demonstrates TCP communication, GUI design, and exception handling, offering efficient class scheduling functionality between client and server applications.





