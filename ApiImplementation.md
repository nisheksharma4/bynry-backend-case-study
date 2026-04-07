# Part 3: API Implementation


@GetMapping("/api/companies/{companyId}/alerts/low-stock")
public ResponseEntity<?> getLowStockAlerts(@PathVariable Long companyId) {

    List<Map<String, Object>> alerts = new ArrayList<>();

    // 1. Get all products for company
    List<Product> products = productRepository.findByCompanyId(companyId);

    for (Product product : products) {

        // 2. Get inventory for each product
        List<Inventory> inventories = inventoryRepository.findByProductId(product.getId());

        for (Inventory inv : inventories) {

            // 3. Check low stock condition
            if (inv.getQuantity() < product.getThreshold()) {

                // 4. Check recent sales (assume last 30 days)
                boolean hasRecentSales = transactionRepository.existsRecentSales(
                        product.getId(), 30
                );

                if (!hasRecentSales) {
                    continue;
                }

                // 5. Get supplier (first supplier for simplicity)
                Supplier supplier = supplierRepository.findFirstByProductId(product.getId());

                // 6. Build alert response
                Map<String, Object> alert = new HashMap<>();
                alert.put("product_id", product.getId());
                alert.put("product_name", product.getName());
                alert.put("sku", product.getSku());
                alert.put("warehouse_id", inv.getWarehouseId());
                alert.put("current_stock", inv.getQuantity());
                alert.put("threshold", product.getThreshold());

                // Simple assumption for stockout
                alert.put("days_until_stockout", 10);

                if (supplier != null) {
                    Map<String, Object> supplierData = new HashMap<>();
                    supplierData.put("id", supplier.getId());
                    supplierData.put("name", supplier.getName());
                    supplierData.put("contact_email", supplier.getContactEmail());

                    alert.put("supplier", supplierData);
                }

                alerts.add(alert);
            }
        }
    }

    // 7. Final response
    Map<String, Object> response = new HashMap<>();
    response.put("alerts", alerts);
    response.put("total_alerts", alerts.size());

    return ResponseEntity.ok(response);
}
