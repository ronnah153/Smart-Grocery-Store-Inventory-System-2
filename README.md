import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.*;
import javafx.stage.Stage;
import javafx.geometry.Insets;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;

/**
 * Main application class for the Smart Grocery Store Inventory System.
 * This application uses JavaFX to create a GUI for managing products.
 */
public class SmartGroceryStoreGUI extends Application {

    // Inventory class to store and manage products.
    static class Product {
        private String productId;
        private String name;
        private double price;
        private int quantity;
        private String category;

        // Constructor to initialize product details
        public Product(String productId, String name, double price, int quantity, String category) {
            this.productId = productId;
            this.name = name;
            this.price = price;
            this.quantity = quantity;
            this.category = category;
        }

        // Getter methods for product attributes
        public String getProductId() { return productId; }
        public String getName() { return name; }
        public double getPrice() { return price; }
        public int getQuantity() { return quantity; }
        public String getCategory() { return category; }

        // Setter for updating product quantity
        public void setQuantity(int quantity) { this.quantity = quantity; }

        // Overridden toString method for displaying product details in the GUI
        @Override
        public String toString() {
            return "ID: " + productId + ", Name: " + name + ", Price: $" + price +
                    ", Quantity: " + quantity + ", Category: " + category;
        }
    }

    // Inventory class to manage product storage and operations
    static class Inventory {
        private HashMap<String, Product> products = new HashMap<>();

        // Adds a new product to the inventory
        public void addProduct(Product product) {
            products.put(product.getProductId(), product);
        }

        // Removes a product by its ID
        public void removeProduct(String productId) {
            products.remove(productId);
        }

        // Retrieves a product by its ID
        public Product getProduct(String productId) {
            return products.get(productId);
        }

        // Retrieves all products as a list
        public List<Product> getAllProducts() {
            return new ArrayList<>(products.values());
        }

        // Retrieves products with low stock (quantity < 5)
        public List<Product> getLowStockProducts() {
            List<Product> lowStock = new ArrayList<>();
            for (Product product : products.values()) {
                if (product.getQuantity() < 5) {
                    lowStock.add(product);
                }
            }
            return lowStock;
        }
    }

    private Inventory inventory = new Inventory(); // Central inventory instance

    @Override
    public void start(Stage primaryStage) {
        // Main application window title
        primaryStage.setTitle("Smart Grocery Store Inventory");

        // TabPane for navigation between different functionalities
        TabPane tabPane = new TabPane();

        // Adding tabs to the application
        Tab tabAdd = createAddProductTab();
        Tab tabView = createViewInventoryTab();
        Tab tabLowStock = createLowStockTab();

        // Add all tabs to the TabPane
        tabPane.getTabs().addAll(tabAdd, tabView, tabLowStock);

        // Set the scene and show the primary stage
        Scene scene = new Scene(tabPane, 600, 400);
        primaryStage.setScene(scene);
        primaryStage.show();
    }

    // Tab for adding products to the inventory
    private Tab createAddProductTab() {
        Tab tab = new Tab("Add Product");
        tab.setClosable(false);

        // Layout for the "Add Product" tab
        GridPane grid = new GridPane();
        grid.setPadding(new Insets(10));
        grid.setVgap(10);
        grid.setHgap(10);

        // Input fields and labels for adding a product
        Label lblId = new Label("Product ID:");
        TextField txtId = new TextField();

        Label lblName = new Label("Name:");
        TextField txtName = new TextField();

        Label lblPrice = new Label("Price:");
        TextField txtPrice = new TextField();

        Label lblQuantity = new Label("Quantity:");
        TextField txtQuantity = new TextField();

        Label lblCategory = new Label("Category:");
        TextField txtCategory = new TextField();

        Button btnAdd = new Button("Add Product");
        Label lblMessage = new Label();

        // Event handling for adding a product
        btnAdd.setOnAction(e -> {
            String id = txtId.getText();
            String name = txtName.getText();
            double price;
            int quantity;
            try {
                price = Double.parseDouble(txtPrice.getText());
                quantity = Integer.parseInt(txtQuantity.getText());
            } catch (NumberFormatException ex) {
                lblMessage.setText("Error: Invalid price or quantity.");
                return;
            }
            String category = txtCategory.getText();

            // Add the product to inventory and confirm
            Product product = new Product(id, name, price, quantity, category);
            inventory.addProduct(product);
            lblMessage.setText("Product added successfully!");
        });

        // Adding components to the layout
        grid.add(lblId, 0, 0);
        grid.add(txtId, 1, 0);
        grid.add(lblName, 0, 1);
        grid.add(txtName, 1, 1);
        grid.add(lblPrice, 0, 2);
        grid.add(txtPrice, 1, 2);
        grid.add(lblQuantity, 0, 3);
        grid.add(txtQuantity, 1, 3);
        grid.add(lblCategory, 0, 4);
        grid.add(txtCategory, 1, 4);
        grid.add(btnAdd, 1, 5);
        grid.add(lblMessage, 1, 6);

        tab.setContent(grid);
        return tab;
    }

    // Tab for viewing all products in the inventory
    private Tab createViewInventoryTab() {
        Tab tab = new Tab("View Inventory");
        tab.setClosable(false);

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(10));

        ListView<String> listView = new ListView<>();
        Button btnRefresh = new Button("Refresh");

        // Refresh inventory list on button click
        btnRefresh.setOnAction(e -> {
            listView.getItems().clear();
            for (Product product : inventory.getAllProducts()) {
                listView.getItems().add(product.toString());
            }
        });

        layout.getChildren().addAll(listView, btnRefresh);
        tab.setContent(layout);
        return tab;
    }

    // Tab for viewing low-stock products
    private Tab createLowStockTab() {
        Tab tab = new Tab("Low Stock");
        tab.setClosable(false);

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(10));

        ListView<String> listView = new ListView<>();
        Button btnCheck = new Button("Check Low Stock");

        // Check low-stock products and display them
        btnCheck.setOnAction(e -> {
            listView.getItems().clear();
            List<Product> lowStockProducts = inventory.getLowStockProducts();
            if (lowStockProducts.isEmpty()) {
                listView.getItems().add("No low-stock products.");
            } else {
                for (Product product : lowStockProducts) {
                    listView.getItems().add(product.toString());
                }
            }
        });

        layout.getChildren().addAll(listView, btnCheck);
        tab.setContent(layout);
        return tab;
    }

    public static void main(String[] args) {
        // Launch the JavaFX application
        launch(args);
    }
}
