# project
CREATE TABLE unit_conversion (
    id INT PRIMARY KEY AUTO_INCREMENT,
    from_unit VARCHAR(50),
    to_unit VARCHAR(50),
    conversion_factor DOUBLE
);

-- Example Data
INSERT INTO unit_conversion (from_unit, to_unit, conversion_factor) VALUES
('meter', 'foot', 3.28084),
('foot', 'meter', 0.3048),
('kilogram', 'pound', 2.20462),
('pound', 'kilogram', 0.453592);

import java.sql.*;
import java.util.Scanner;

public class UnitConverter {

    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/unitdb"; // Replace with your DB name
        String user = "root"; // Replace with your DB user
        String password = "yourpassword"; // Replace with your DB password

        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter value: ");
        double value = scanner.nextDouble();

        System.out.print("From unit (e.g., meter): ");
        String fromUnit = scanner.next().toLowerCase();

        System.out.print("To unit (e.g., foot): ");
        String toUnit = scanner.next().toLowerCase();

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            String query = "SELECT conversion_factor FROM unit_conversion WHERE from_unit = ? AND to_unit = ?";
            try (PreparedStatement stmt = conn.prepareStatement(query)) {
                stmt.setString(1, fromUnit);
                stmt.setString(2, toUnit);

                ResultSet rs = stmt.executeQuery();
                if (rs.next()) {
                    double factor = rs.getDouble("conversion_factor");
                    double result = value * factor;
                    System.out.printf("%.4f %s = %.4f %s%n", value, fromUnit, result, toUnit);
                } else {
                    System.out.println("Conversion not found in database.");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
