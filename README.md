# PROG5121_Part1
Part1 Submission
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 */

package com.mycompany.prog5121part1;
import java.util.Scanner;
import java.util.ArrayList;


/**
 *
 * @author growwithbella
 */
public class Prog5121Part1 {

    public static void main(String[] args) {
        
        System.out.println("PROGRAM HAS STARTED!");
        
        Scanner scanner = new Scanner(System.in);
        
         // Store registered users
        ArrayList<Login> users = new ArrayList<>();

        System.out.println("\nWelcome to our Chat App 2026!");

        while (true) {

            // Display the main menu
            System.out.println("\n===== MAIN MENU =====");
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");

            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {

                case 1:
                    // =========================
                    // REGISTRATION
                    // =========================

                    System.out.println("\n===== REGISTRATION =====");

                    System.out.print("Enter username: ");
                    String username = scanner.nextLine();

                    // Validate username
                    boolean validUsername =
                            username.contains("_")
                            && username.length() <= 5;

                    if (!validUsername) {

                        System.out.println(
                                "Username is not correctly formatted, " +
                                "please ensure that your username contains " +
                                "an underscore and is no more than 5 characters.");

                        break;
                    }

                    System.out.println("Username successfully captured!");

                    // Enter password
                    System.out.print("Enter password: ");
                    String password = scanner.nextLine();

                    // Validate password
                    boolean validPassword =
                            password.length() >= 8
                            && password.matches(".*[A-Z].*")
                            && password.matches(".*[0-9].*")
                            && password.matches(
                                    ".*[!@#$%^&*(),.?\":{}|<>].*");

                    if (!validPassword) {

                        System.out.println(
                                "Password is not correctly formatted, " +
                                "please ensure that the password contains " +
                                "at least 8 characters, a capital letter, " +
                                "a number and a special character.");

                        break;
                    }

                    System.out.println("Password successfully captured!");

                    // Enter cellphone number
                    System.out.print(
                            "Enter South African cellphone number " +
                            "(e.g. +27831111111): ");

                    String phoneNumber = scanner.nextLine();

                    // Validate cellphone number
                    boolean validPhoneNumber =
                            phoneNumber.matches("^\\+27[6-8][0-9]{8}$");

                    if (!validPhoneNumber) {

                        System.out.println(
                                "Cell phone number is incorrectly formatted " +
                                "or does not contain the international code.");

                        break;
                    }

                    System.out.println(
                            "Cellphone number successfully captured!");

                    // Check if username already exists
                    boolean userExists = false;

                    for (Login user : users) {

                        if (user.getUsername().equals(username)) {
                            userExists = true;
                            break;
                        }
                    }

                    if (userExists) {

                        System.out.println(
                                "Username already exists. " +
                                "Please choose another username.");

                        break;
                    }

                    // Create a new user
                    Login newUser =
                            new Login(username, password, phoneNumber);

                    // Add user to ArrayList
                    users.add(newUser);

                    System.out.println("User registered successfully!");

                    System.out.println(
                            "Welcome " + username +
                            "! You can now log in.");

                    // Automatically take user to login
                    promptLogin(scanner, users);

                    break;

                case 2:
                    // =========================
                    // LOGIN
                    // =========================

                    System.out.println("\n===== LOGIN =====");

                    if (users.isEmpty()) {

                        System.out.println(
                                "No users are registered yet. " +
                                "Please register first.");

                        break;
                    }

                    promptLogin(scanner, users);

                    break;

                case 3:
                    // =========================
                    // EXIT
                    // =========================

                    System.out.println("Goodbye!");

                    scanner.close();

                    return;

                default:

                    System.out.println(
                            "Invalid option. Please choose 1, 2 or 3.");
            }
        }
    }

    // ==========================================
    // LOGIN METHOD
    // ==========================================

    private static void promptLogin(
            Scanner scanner,
            ArrayList<Login> users) {

        System.out.print("Enter username: ");
        String enteredUsername = scanner.nextLine();

        System.out.print("Enter password: ");
        String enteredPassword = scanner.nextLine();

        // Find the registered user
        Login user = null;

        for (Login registeredUser : users) {

            if (registeredUser.getUsername()
                    .equals(enteredUsername)) {

                user = registeredUser;
                break;
            }
        }

        // Check login details
        if (user != null) {

            boolean loginStatus =
                    user.loginUser(enteredPassword);

            System.out.println(
                    user.returnLoginStatus(loginStatus));

        } else {

            System.out.println(
                    "Username or password incorrect, please try again.");
        }
    }
}

/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package com.mycompany.prog5121part1;

import java.util.Scanner;
import java.util.ArrayList;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;

/**
 *
 * @author growwithbella
 */
public class Login {

    private String username;
    private String passwordHash;
    private String phoneNumber;
    private byte[] salt;

    // Constructor
    public Login(
            String username,
            String password,
            String phoneNumber) {

        this.username = username;
        this.phoneNumber = phoneNumber;

        // Generate a unique salt
        this.salt = generateSalt();

        // Hash the password
        this.passwordHash = hashPassword(password, salt);
    }
    
    // CHECK USERNAME

    public boolean checkUserName() {

        return username.contains("_")
                && username.length() <= 5;
    }

    // CHECK PASSWORD COMPLEXITY

    public boolean checkPasswordComplexity(String password) {

        return password.length() >= 8
                && password.matches(".*[A-Z].*")
                && password.matches(".*[0-9].*")
                && password.matches(".*[!@#$%^&*(),.?\":{}|<>].*");
    }

    // ==========================================
    // CHECK CELLPHONE NUMBER
    // ==========================================

    public boolean checkCellPhoneNumber() {

        return phoneNumber.matches("^\\+27[6-8][0-9]{8}$");
    }

    // GET USERNAME

    public String getUsername() {
        return username;
    }

    // LOGIN USER

    public boolean loginUser(String enteredPassword) {

        // Hash the entered password using
        // the same salt used during registration
        String enteredPasswordHash =
                hashPassword(enteredPassword, salt);

        // Compare the two hashes
        return this.passwordHash.equals(enteredPasswordHash);
    }

    // ==========================================
    // LOGIN STATUS
    // ==========================================

    public String returnLoginStatus(boolean status) {

        if (status) {
            return "Login successful!";
        } else {
            return "Username or password incorrect, please try again.";
        }
    }

    // ==========================================
    // HASH PASSWORD
    // ==========================================

    private String hashPassword(
            String password,
            byte[] salt) {

        try {

            MessageDigest md =
                    MessageDigest.getInstance("SHA-256");

            // Add salt to the password
            md.update(salt);

            // Hash the password
            byte[] hash =
                    md.digest(password.getBytes());

            // Convert hash to hexadecimal
            StringBuilder hexString =
                    new StringBuilder();

            for (byte b : hash) {

                hexString.append(
                        String.format("%02x", b));
            }

            return hexString.toString();

        } catch (NoSuchAlgorithmException e) {

            throw new RuntimeException(
                    "Error hashing password", e);
        }
    }

    // ==========================================
    // GENERATE RANDOM SALT
    // ==========================================

    private byte[] generateSalt() {

        SecureRandom random =
                new SecureRandom();

        byte[] salt = new byte[16];

        random.nextBytes(salt);

        return salt;
    }
}
