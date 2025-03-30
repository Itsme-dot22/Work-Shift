# Work-Shift
import java.util.*;

public class EmployeeScheduler {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        // Step 1: Input and Storage
        Map<String, Employee> employees = new HashMap<>();
        System.out.print("Enter number of employees: ");
        int numEmployees = scanner.nextInt();
        scanner.nextLine();
        
        for (int i = 0; i < numEmployees; i++) {
            System.out.print("Enter employee name: ");
            String name = scanner.nextLine();
            System.out.print("Enter preferred shifts (morning, afternoon, evening): ");
            String[] preferences = scanner.nextLine().split(" ");
            employees.put(name, new Employee(name, preferences));
        }
        
        // Step 2 & Step 3: Scheduling Logic and Conflict Resolution
        Map<String, Map<String, List<String>>> schedule = initializeSchedule();
        assignShifts(employees, schedule);
        
        // Step 4: Output Schedule
        outputSchedule(schedule);
        
        scanner.close();
    }
    
    private static Map<String, Map<String, List<String>>> initializeSchedule() {
        Map<String, Map<String, List<String>>> schedule = new HashMap<>();
        String[] days = {"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"};
        
        for (String day : days) {
            schedule.put(day, new HashMap<>());
            schedule.get(day).put("morning", new ArrayList<>());
            schedule.get(day).put("afternoon", new ArrayList<>());
            schedule.get(day).put("evening", new ArrayList<>());
        }
        
        return schedule;
    }
    
    private static void assignShifts(Map<String, Employee> employees,
                                     Map<String, Map<String, List<String>>> schedule) {
        Random random = new Random();
        
        for (String day : schedule.keySet()) {
            Map<String, List<String>> shifts = schedule.get(day);
            
            for (String shift : shifts.keySet()) {
                List<Employee> availableEmployees = getAvailableEmployees(employees);
                
                while (shifts.get(shift).size() < 2 && !availableEmployees.isEmpty()) {
                    Employee employee = availableEmployees.remove(random.nextInt(availableEmployees.size()));
                    shifts.get(shift).add(employee.name);
                    employee.assignedDays++;
                }
            }
        }
    }
    
    private static List<Employee> getAvailableEmployees(Map<String, Employee> employees) {
        List<Employee> availableEmployees = new ArrayList<>();
        
        for (Employee employee : employees.values()) {
            if (employee.assignedDays < 5) {
                availableEmployees.add(employee);
            }
        }
        
        return availableEmployees;
    }
    
    private static void outputSchedule(Map<String, Map<String, List<String>>> schedule) {
        System.out.println("\nFinal Schedule:");
        
        for (String day : schedule.keySet()) {
            System.out.println(day + ":");
            
            Map<String, List<String>> shifts = schedule.get(day);
            
            for (String shift : shifts.keySet()) {
                System.out.println("  " + shift + ": " + String.join(", ", shifts.get(shift)));
            }
        }
    }
}

class Employee {
    String name;
    String[] preferences;
    int assignedDays;
    
    Employee(String name, String[] preferences) {
        this.name = name;
        this.preferences = preferences;
        this.assignedDays = 0;
    }
}
