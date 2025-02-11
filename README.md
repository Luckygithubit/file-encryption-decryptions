# file-encryption-decryptions
Its is a tool that can hide or lock your personal data to another person.
package imageoperation2;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;

public class fileoperation3 {

    private static int key; // Encryption/decryption key

    public static void main(String[] args) {
        SwingUtilities.invokeLater(fileoperation3::createAndShowGUI);
    }

    private static void createAndShowGUI() {
        JFrame frame = new JFrame("File Encryption/Decryption");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(500, 300);
        frame.setLocationRelativeTo(null);

        JPanel mainPanel = new JPanel(new BorderLayout());
        mainPanel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));

        // Panel for key input and buttons
        JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 10, 10));

        JLabel keyLabel = new JLabel("Enter Key:");
        JTextField keyField = new JTextField(10);
        keyField.setFont(new Font("Arial", Font.PLAIN, 16));

        JButton encryptButton = new JButton("Encrypt");
        JButton decryptButton = new JButton("Decrypt");

        encryptButton.setFont(new Font("Arial", Font.BOLD, 16));
        decryptButton.setFont(new Font("Arial", Font.BOLD, 16));

        // Add components to input panel
        inputPanel.add(keyLabel);
        inputPanel.add(keyField);
        inputPanel.add(encryptButton);
        inputPanel.add(decryptButton);

        // Text area to display messages
        JTextArea messageArea = new JTextArea(10, 40);
        messageArea.setEditable(false);
        messageArea.setFont(new Font("Arial", Font.PLAIN, 14));
        messageArea.setLineWrap(true);
        JScrollPane scrollPane = new JScrollPane(messageArea);

        // Add panels to main panel
        mainPanel.add(inputPanel, BorderLayout.NORTH);
        mainPanel.add(scrollPane, BorderLayout.CENTER);

        // Action listener for Encrypt button
        encryptButton.addActionListener((ActionEvent e) -> {
            processFiles(keyField.getText(), true, messageArea);
        });

        // Action listener for Decrypt button
        decryptButton.addActionListener((ActionEvent e) -> {
            processFiles(keyField.getText(), false, messageArea);
        });

        frame.getContentPane().add(mainPanel);
        frame.setVisible(true);
    }

    private static void processFiles(String keyText, boolean encryptMode, JTextArea messageArea) {
        try {
            key = Integer.parseInt(keyText);

            JFileChooser fileChooser = new JFileChooser();
            fileChooser.setMultiSelectionEnabled(true);
            int result = fileChooser.showOpenDialog(null);

            if (result == JFileChooser.APPROVE_OPTION) {
                File[] selectedFiles = fileChooser.getSelectedFiles();

                for (File file : selectedFiles) {
                    FileInputStream fis = new FileInputStream(file);
                    byte[] data = new byte[(int) file.length()];
                    fis.read(data);
                    fis.close();

                    for (int i = 0; i < data.length; i++) {
                        data[i] = (byte) (data[i] ^ key); // Encrypt or decrypt
                    }

                    FileOutputStream fos = new FileOutputStream(file);
                    fos.write(data);   
                    fos.close();
                }

                String operation = encryptMode ? "Encryption" : "Decryption";
                messageArea.append(operation + " completed successfully for selected files.\n");
            } else {
                messageArea.append("Operation cancelled by user.\n");
            }

        } catch (NumberFormatException | NullPointerException ex) {
            messageArea.append("Invalid key. Please enter a valid integer key.\n");
        } catch (Exception ex) {
            messageArea.append("Error processing files: " + ex.getMessage() + "\n");
            ex.printStackTrace();
        }
    }
}
