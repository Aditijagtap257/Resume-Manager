# Resume-Manager
import java.util.*;
import java.io.*;
import java.util.regex.*;

class Resume {
    String name;
    String skills;
    int experience;

    public Resume(String name, String skills, int experience) {
        this.name = name;
        this.skills = skills;
        this.experience = experience;
    }

    @Override
    public String toString() {
        return "Name: " + name + ", Skills: " + skills + ", Experience: " + experience + " years";
    }
}

class ResumeManager {
    private LinkedList<Resume> resumes;

    public ResumeManager() {
        this.resumes = new LinkedList<>();
    }

    public void importResumesFromFile(String filePath) {
        try (BufferedReader br = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = br.readLine()) != null) {
                extractResumeData(line);
            }
            System.out.println("Resumes imported successfully from file.");
        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());
        }
    }

    private void extractResumeData(String line) {
        // Regex to find experience (e.g., "3 years", "5 years experience")
        Pattern experiencePattern = Pattern.compile("(\\d+)\\s*(?:years|yrs|year|yr)", Pattern.CASE_INSENSITIVE);
        Matcher experienceMatcher = experiencePattern.matcher(line);
        
        int experience = 0;
        if (experienceMatcher.find()) {
            experience = Integer.parseInt(experienceMatcher.group(1));
        }

        // Extract skills by searching for keywords
        List<String> commonSkills = Arrays.asList("Java", "Python", "C++", "JavaScript", "React", "SQL", "AI", "ML");
        StringBuilder skills = new StringBuilder();
        for (String skill : commonSkills) {
            if (line.toLowerCase().contains(skill.toLowerCase())) {
                if (skills.length() > 0) skills.append(", ");
                skills.append(skill);
            }
        }

        // Extract name (first word assumed to be name)
        String name = line.split(" ")[0];

        if (!skills.toString().isEmpty() && experience > 0) {
            resumes.add(new Resume(name, skills.toString(), experience));
        }
    }

    public void searchBySkill(String skill) {
        boolean found = false;
        for (Resume r : resumes) {
            if (r.skills.toLowerCase().contains(skill.toLowerCase())) {
                System.out.println(r);
                found = true;
            }
        }
        if (!found) {
            System.out.println("No resumes found with skill: " + skill);
        }
    }

    public void sortByExperience() {
        resumes.sort(Comparator.comparingInt(r -> -r.experience)); // Descending order
        System.out.println("Resumes sorted by experience:");
        displayResumes();
    }

    public void displayResumes() {
        if (resumes.isEmpty()) {
            System.out.println("No resumes available.");
            return;
        }
        for (Resume r : resumes) {
            System.out.println(r);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        ResumeManager manager = new ResumeManager();

        System.out.print("Enter File Path: ");
        String filePath = scanner.nextLine();
        manager.importResumesFromFile(filePath);
        
        while (true) {
            System.out.println("\nResume Manager System");
            System.out.println("1. Search by Skill");
            System.out.println("2. Sort by Experience");
            System.out.println("3. Display All Resumes");
            System.out.println("4. Exit");
            System.out.print("Choose an option: ");
            
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    System.out.print("Enter Skill to Search: ");
                    String skill = scanner.nextLine();
                    manager.searchBySkill(skill);
                    break;
                case 2:
                    manager.sortByExperience();
                    break;
                case 3:
                    manager.displayResumes();
                    break;
                case 4:
                    System.out.println("Exiting...");
                    scanner.close();
                    return;
                default:
                    System.out.println("Invalid option. Try again.");
            }
        }
    }
}
