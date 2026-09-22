import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.sql.*;

public class StudentManagementSystem extends JFrame {
    Connection con;
    PreparedStatement pst;
    ResultSet rs;

    JTextField txtRoll, txtName, txtPer;
    JButton btnAdd, btnSearch, btnDelete, btnDisplay;
    JTextArea output;

    public StudentManagementSystem() {
        setTitle("Student Management System");
        setSize(400, 400);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new FlowLayout());

        txtRoll = new JTextField(10);
        txtName = new JTextField(10);
        txtPer = new JTextField(10);

        btnAdd = new JButton("Add");
        btnSearch = new JButton("Search");
        btnDelete = new JButton("Delete");
        btnDisplay = new JButton("Display All");

        output = new JTextArea(10, 30);

        add(new JLabel("Roll No:")); add(txtRoll);
        add(new JLabel("Name:")); add(txtName);
        add(new JLabel("Percentage:")); add(txtPer);

        add(btnAdd); add(btnSearch); add(btnDelete); add(btnDisplay);
        add(new JScrollPane(output));

        connectDB();

        btnAdd.addActionListener(e -> addStudent());
        btnSearch.addActionListener(e -> searchStudent());
        btnDelete.addActionListener(e -> deleteStudent());
        btnDisplay.addActionListener(e -> displayStudents());
    }

    void connectDB() {
        try {
            con = DriverManager.getConnection("jdbc:mysql://localhost/studentdb", "root", "password");
        } catch (Exception ex) {
            output.setText("Error connecting to DB: " + ex.getMessage());
        }
    }

    void addStudent() {
        try {
            pst = con.prepareStatement("INSERT INTO students VALUES(?,?,?)");
            pst.setInt(1, Integer.parseInt(txtRoll.getText()));
            pst.setString(2, txtName.getText());
            pst.setFloat(3, Float.parseFloat(txtPer.getText()));
            pst.executeUpdate();
            output.setText("Student Added Successfully!");
        } catch (Exception ex) {
            output.setText("Error: " + ex.getMessage());
        }
    }

    void searchStudent() {
        try {
            pst = con.prepareStatement("SELECT * FROM students WHERE rollno=?");
            pst.setInt(1, Integer.parseInt(txtRoll.getText()));
            rs = pst.executeQuery();
            if (rs.next()) {
                output.setText("Found: " + rs.getInt(1) + " " + rs.getString(2) + " " + rs.getFloat(3));
            } else {
                output.setText("No Record Found!");
            }
        } catch (Exception ex) {
            output.setText("Error: " + ex.getMessage());
        }
    }

    void deleteStudent() {
        try {
            pst = con.prepareStatement("DELETE FROM students WHERE rollno=?");
            pst.setInt(1, Integer.parseInt(txtRoll.getText()));
            int rows = pst.executeUpdate();
            if (rows > 0) output.setText("Record Deleted!");
            else output.setText("No Record Found!");
        } catch (Exception ex) {
            output.setText("Error: " + ex.getMessage());
        }
    }

    void displayStudents() {
        try {
            pst = con.prepareStatement("SELECT * FROM students");
            rs = pst.executeQuery();
            StringBuilder sb = new StringBuilder("Roll | Name | Percentage\n");
            while (rs.next()) {
                sb.append(rs.getInt(1)).append(" | ")
                  .append(rs.getString(2)).append(" | ")
                  .append(rs.getFloat(3)).append("\n");
            }
            output.setText(sb.toString());
        } catch (Exception ex) {
            output.setText("Error: " + ex.getMessage());
        }
    }

    public static void main(String[] args) {
        new StudentManagementSystem().setVisible(true);
    }
}
# StudentManagementSystem1
