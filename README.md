import java.util.*;

class User {
    protected int id;
    protected String name;
    protected String location;

    User(int id, String name, String location) {
        this.id = id;
        this.name = name;
        this.location = location;
    }

    void display() {
        System.out.println("ID: " + id);
        System.out.println("Name: " + name);
        System.out.println("Location: " + location);
    }
}

class FoodProvider extends User {

    FoodProvider(int id, String name, String location) {
        super(id, name, location);
    }

    void addFood(List<Food> foods, Food food) {
        foods.add(food);
        System.out.println("Food listed successfully!");
    }
}

class Receiver extends User {

    Receiver(int id, String name, String location) {
        super(id, name, location);
    }

    void requestFood(Food food, int quantity) {

        if (food != null &&
            food.quantity >= quantity &&
            food.status.equals("Available")) {

            food.quantity -= quantity;

            if (food.quantity == 0) {
                food.status = "Rescued";
            } else {
                food.status = "Requested";
            }

            System.out.println("Food request submitted successfully!");

        } else {
            System.out.println("Food is not available.");
        }
    }
}

class Admin extends User {

    Admin(int id, String name, String location) {
        super(id, name, location);
    }

    void verifyUser(User user) {
        System.out.println(user.name + " verified by Admin.");
    }

    void removeExpiredFood(List<Food> foods) {
        foods.removeIf(food -> food.status.equals("Expired"));
        System.out.println("Expired food removed.");
    }
}

class Food {

    int id;
    String name;
    String category;
    int quantity;
    String availability;
    String location;
    String status;

    Food(int id, String name, String category, int quantity,
         String availability, String location) {

        this.id = id;
        this.name = name;
        this.category = category;
        this.quantity = quantity;
        this.availability = availability;
        this.location = location;
        this.status = "Available";
    }

    void display() {
        System.out.println("----------------------------");
        System.out.println("Food ID      : " + id);
        System.out.println("Food Name    : " + name);
        System.out.println("Category     : " + category);
        System.out.println("Quantity     : " + quantity);
        System.out.println("Availability : " + availability);
        System.out.println("Location     : " + location);
        System.out.println("Status       : " + status);
    }
}

class FoodMatcher {

    int calculateScore(Food food, Receiver receiver,
                       String requiredCategory, int requiredQuantity) {

        int score = 0;

        // Category match
        if (food.category.equalsIgnoreCase(requiredCategory)) {
            score++;
        }

        // Quantity match
        if (food.quantity >= requiredQuantity) {
            score++;
        }

        // Location match
        if (food.location.equalsIgnoreCase(receiver.location)) {
            score++;
        }

        // Availability match
        if (food.status.equals("Available")) {
            score++;
        }

        return score;
    }

    Food findMatches(List<Food> foods, Receiver receiver,
                     String category, int quantity) {

        Food bestMatch = null;
        int highestScore = 0;

        for (Food food : foods) {

            if (!food.status.equals("Available")) {
                continue;
            }

            int score = calculateScore(
                    food,
                    receiver,
                    category,
                    quantity
            );

            if (score > highestScore) {
                highestScore = score;
                bestMatch = food;
            }
        }

        if (bestMatch != null) {
            System.out.println("\nBest Matching Food:");
            bestMatch.display();

            System.out.println("Match Score: "
                    + highestScore + "/4");
        } else {
            System.out.println("No suitable food found.");
        }

        return bestMatch;
    }
}

public class Main {

    public static void main(String[] args) {

        Scanner sc = new Scanner(System.in);

        List<Food> foods = new ArrayList<>();

        FoodProvider provider =
                new FoodProvider(
                        1,
                        "ABC Restaurant",
                        "Coimbatore"
                );

        Receiver receiver =
                new Receiver(
                        2,
                        "Helping Hands NGO",
                        "Coimbatore"
                );

        Admin admin =
                new Admin(
                        3,
                        "Admin",
                        "Coimbatore"
                );

        // Admin verifies users
        admin.verifyUser(provider);
        admin.verifyUser(receiver);

        // ==============================
        // FOOD PROVIDER
        // ==============================

        System.out.println("\n===== FOOD PROVIDER =====");

        System.out.print("Enter food name: ");
        String foodName = sc.nextLine();

        System.out.print("Enter category: ");
        String category = sc.nextLine();

        System.out.print("Enter quantity: ");
        int quantity = sc.nextInt();
        sc.nextLine();

        System.out.print("Enter availability period: ");
        String availability = sc.nextLine();

        System.out.print("Enter pickup location: ");
        String location = sc.nextLine();

        Food food = new Food(
                101,
                foodName,
                category,
                quantity,
                availability,
                location
        );

        provider.addFood(foods, food);

        // ==============================
        // AVAILABLE FOOD
        // ==============================

        System.out.println("\n===== AVAILABLE FOOD =====");

        for (Food f : foods) {
            f.display();
        }

        // ==============================
        // FOOD MATCHING
        // ==============================

        System.out.println("\n===== FOOD MATCHING =====");

        System.out.print("Enter required food category: ");
        String requiredCategory = sc.nextLine();

        System.out.print("Enter required quantity: ");
        int requiredQuantity = sc.nextInt();
        sc.nextLine();

        FoodMatcher matcher = new FoodMatcher();

        Food matchedFood = matcher.findMatches(
                foods,
                receiver,
                requiredCategory,
                requiredQuantity
        );

        // ==============================
        // FOOD REQUEST
        // ==============================

        System.out.println("\n===== FOOD REQUEST =====");

        if (matchedFood != null) {

            System.out.print(
                    "Do you want to request this food? (yes/no): "
            );

            String choice = sc.nextLine();

            if (choice.equalsIgnoreCase("yes")) {

                receiver.requestFood(
                        matchedFood,
                        requiredQuantity
                );

                System.out.println(
                        "Food collection completed."
                );

            } else {

                System.out.println(
                        "Food request cancelled."
                );
            }

        } else {

            System.out.println(
                    "No suitable food available for request."
            );
        }

        // ==============================
        // FINAL STATUS
        // ==============================

        System.out.println("\n===== FINAL STATUS =====");

        if (matchedFood != null) {
            matchedFood.display();
        }

        sc.close();
    }
}
