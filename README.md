# Car-Parking-Management-System
Oop project with java

public interface IParkable {
    void park();
    void remove();
}
abstract class Vehicle {
    protected String licensePlate;
    protected String ownerName;

    public Vehicle(String licensePlate, String ownerName) {
        this.licensePlate = licensePlate;
        this.ownerName = ownerName;
    }

    public String getLicensePlate() {
        return licensePlate;
    }

    public String getOwnerName() {
        return ownerName;
    }

    public abstract void park();

    public abstract void remove();
}
public class Car extends Vehicle implements IParkable {
    private String contactNumber;

    public Car(String licensePlate, String ownerName, String contactNumber) {
        super(licensePlate, ownerName);
        this.contactNumber = contactNumber;
    }

    public String getContactNumber() {
        return contactNumber;
    }

    public void setContactNumber(String contactNumber) {
        this.contactNumber = contactNumber;
    }

    @Override
    public void park() {
        System.out.println("Car parked.");
    }

    @Override
    public void remove() {
        System.out.println("Car removed.");
    }
}
public class ParkingSlot implements IParkable {
    private int slotNumber;
    private boolean isOccupied;
    private Vehicle parkedVehicle;

    public ParkingSlot(int slotNumber) {
        this.slotNumber = slotNumber;
        this.isOccupied = false;
    }

    public int getSlotNumber() {
        return slotNumber;
    }

    public boolean isOccupied() {
        return isOccupied;
    }

    public void setOccupied(boolean isOccupied) {
        this.isOccupied = isOccupied;
    }

    public Vehicle getParkedVehicle() {
        return parkedVehicle;
    }

    public void setParkedVehicle(Vehicle parkedVehicle) {
        this.parkedVehicle = parkedVehicle;
    }

    @Override
    public void park() {
        this.isOccupied = true;
        System.out.println("Slot " + slotNumber + " is now occupied.");
    }

    @Override
    public void remove() {
        this.isOccupied = false;
        this.parkedVehicle = null;
        System.out.println("Slot " + slotNumber + " is now free.");
    }
}

import java.util.*;

public class ParkingManager {
    private List<ParkingSlot> parkingSlots;
    private Map<String, ParkingSlot> parkedVehicles;

    public ParkingManager(int numberOfSlots) {
        parkingSlots = new ArrayList<>();
        for (int i = 1; i <= numberOfSlots; i++) {
            parkingSlots.add(new ParkingSlot(i));
        }
        parkedVehicles = new HashMap<>();
    }

public boolean parkVehicle(Vehicle vehicle) {
    for (int i = 0; i < parkingSlots.size(); i++) {
        ParkingSlot slot = parkingSlots.get(i);
        if (!slot.isOccupied()) {
            slot.setParkedVehicle(vehicle);
            slot.park();
            parkedVehicles.put(vehicle.getLicensePlate(), slot);
            return true;
        }
    }
    return false;
}


    public boolean removeVehicle(String licensePlate) {
        if (parkedVehicles.containsKey(licensePlate)) {
            ParkingSlot slot = parkedVehicles.remove(licensePlate);
            slot.remove();
            return true;
        }
        return false;
    }

public void checkParkingStatus() {
    for (int i = 0; i < parkingSlots.size(); i++) {
        ParkingSlot slot = parkingSlots.get(i);
        if (slot.isOccupied()) {
            Vehicle vehicle = slot.getParkedVehicle();
            System.out.println("Slot " + slot.getSlotNumber() + ": Occupied | License Plate: " + vehicle.getLicensePlate() + " | Owner: " + vehicle.getOwnerName());
        } else {
            System.out.println("Slot " + slot.getSlotNumber() + ": Free");
        }
    }
}

}
public class InvalidInputException extends Exception{
   
}

import java.io.*;
import java.util.Scanner;
import java.util.Formatter;

public class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return username;
    }

    public boolean login() throws InvalidInputException{
        try (Scanner scanner = new Scanner(new File("F:\\ahmed.txt"))) {
            while (scanner.hasNextLine()) {
                String[] credentials = scanner.nextLine().split(",");
                if (credentials[0].equals(username) && credentials[1].equals(password)) {
                    return true;
                }
            }
            
            throw new InvalidInputException();
        }
        catch (InvalidInputException e){
            System.out.println("username or Password is incorrect ");
        }
        
        
        catch (FileNotFoundException e) {
            System.out.println("error in reading");
        }
        return false;
    }

    public boolean register() {
        try (Formatter out = new Formatter(new FileWriter("F:\\ahmed.txt", true))) {
            out.format("%s,%s%n",username,password);
            return true;
        } catch (IOException e) {
            System.out.println("error in writing");
        }
        return false;
    }
}
import java.util.Scanner;

public class Main {
    public static void main(String[] args) throws InvalidInputException {
        Scanner scanner = new Scanner(System.in);
        ParkingManager parkingManager = new ParkingManager(10);
while (true) {
        System.out.println("Welcome to the Car Parking Management System");
        System.out.println("1. Register");
        System.out.println("2. Login");

        int choice = scanner.nextInt();
        scanner.nextLine();  
        
    while (true) {
        if (choice == 1) {
            System.out.println("Register");
            System.out.print("Username: ");
            String username = scanner.nextLine();
            System.out.print("Password: ");
            String password = scanner.nextLine();
            User user = new User(username, password);
            if (user.register()) {
                System.out.println("Registration successful.");
                break;
            } else {
                System.out.println("Registration failed.");
                break;
            }
        } else {
            if (choice == 2) {
                System.out.println("Login");
                System.out.print("Username: ");
                String username = scanner.nextLine();
                System.out.print("Password: ");
                String password = scanner.nextLine();
                User user = new User(username, password);
                if (user.login()) {
                    System.out.println("Login successful.");
                   
                    while (true) {
                        System.out.println("1. Park Vehicle");
                        System.out.println("2. Remove Vehicle");
                        System.out.println("3. Check Parking Status");
                        System.out.println("4. Exit");
                        int option = scanner.nextInt();
                        scanner.nextLine();  // Consume newline
                        switch (option) {
                            case 1:
                                System.out.print("License Plate: ");
                                String licensePlate = scanner.nextLine();
                                System.out.print("Owner Name: ");
                                String ownerName = scanner.nextLine();
                                System.out.print("Contact Number: ");
                                String contactNumber = scanner.nextLine();
                                Car car = new Car(licensePlate, ownerName, contactNumber);
                                if (parkingManager.parkVehicle(car)) {
                                    System.out.println("Vehicle parked successfully.");
                                } else {
                                    System.out.println("Parking is full.");
                                }
                                break;
                            case 2:
                                System.out.print("License Plate: ");
                                String lp = scanner.nextLine();
                                if (parkingManager.removeVehicle(lp)) {
                                    System.out.println("Vehicle removed successfully.");
                                } else {
                                    System.out.println("Vehicle not found.");
                                }
                                break;
                            case 3:
                                parkingManager.checkParkingStatus();
                                break;
                            case 4:
                                System.out.println("Exiting...");
                                return;
                            default:
                                System.out.println("Invalid option.");
                        }
                    }
                } else {
                    System.out.println("Login failed.");
                }
            }
        }
    }
}

    }

}
