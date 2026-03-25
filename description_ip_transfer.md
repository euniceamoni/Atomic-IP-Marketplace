# Issue Description: IP Ownership Transfer Mechanism (#27)

## Problem Summary
Previously, the `IpRegistry` contract lacked a mechanism to transfer ownership of a registered IP listing. Once registered, a listing was permanently tied to the original owner's address. This limitation blocked secondary market activities, licensing arrangements, and general flexibility in IP management.

## Solution
I have implemented the `transfer_listing` function to allow secure ownership transfer:
1. **Ownership Validation**: The function requires the current owner's cryptographic authorization (`require_auth`) before allowing any changes.
2. **Atomic Storage Update**: The `Listing` object's owner field is updated in persistent storage.
3. **Index Management**: The `OwnerIndex` (a collection of listing IDs for each address) is updated for both the old and new owners to ensure that `list_by_owner` returns accurate results.
4. **Event Emission**: A `transfer` event is emitted, providing a verifiable off-chain record of the transaction.
5. **Structured Errors**: New error variants `ListingNotFound` and `Unauthorized` were added to the `ContractError` enum for clearer failure reporting.

## Technical Details
- **Function**: `transfer_listing(listing_id: u64, new_owner: Address)`
- **Event Topics**: `("transfer", listing_id)`
- **Event Data**: `(old_owner, new_owner)`

## Verification
- Added `test_transfer_listing` to verify successful transfer and index updates.
- Added `test_transfer_listing_not_found` to verify appropriate failure for non-existent IDs.
- Ran `cargo test -p ip_registry`; all tests passed.

issue #27
