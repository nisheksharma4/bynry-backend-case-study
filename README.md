#Part 1 : Code Review & Debugging
##Inventory Management System for B2B SaaS

## ✅ Corrected Implementation (Spring Boot Version)

### 🔧 Improved Version (Java - Spring Boot)

@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductRepository productRepository;

    @Autowired
    private InventoryRepository inventoryRepository;

    @PostMapping
    @Transactional
    public ResponseEntity<?> createProduct(@RequestBody ProductRequest request) {

        // 1. Basic validation
        if (request.getName() == null || request.getSku() == null || request.getPrice() == null) {
            return ResponseEntity.badRequest().body("Missing required fields");
        }

        // Check price is not negative
        if (request.getPrice() < 0) {
            return ResponseEntity.badRequest().body("Invalid price");
        }

        // 2. Check SKU uniqueness
        if (productRepository.existsBySku(request.getSku())) {
            return ResponseEntity.badRequest().body("SKU already exists");
        }

        // 3. Save Product
        Product product = new Product();
        product.setName(request.getName());
        product.setSku(request.getSku());
        product.setPrice(request.getPrice());

        productRepository.save(product);

        // 4. Save Inventory 
        if (request.getWarehouseId() != null) {
            Inventory inventory = new Inventory();
            inventory.setProductId(product.getId());
            inventory.setWarehouseId(request.getWarehouseId());

            if (request.getInitialQuantity() != null) {
                inventory.setQuantity(request.getInitialQuantity());
            } else {
                inventory.setQuantity(0);
            }

            inventoryRepository.save(inventory);
        }

        // 5. Simple response
        return ResponseEntity.ok("Product created successfully");
    }
}
