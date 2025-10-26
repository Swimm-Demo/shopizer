---
title: Removing a Tax Class
---
This document describes the process for removing a Tax Class from the system. The flow ensures only valid and existing Tax Classes are deleted, helping maintain catalog integrity. Input is a request to delete a Tax Class by its unique ID; output is the removal of the specified Tax Class if it exists.

# Initiating Tax Class Removal

This section governs the initiation of the process to remove a Tax Class from the system. The main product role is to ensure that only valid and existing Tax Classes are considered for removal, maintaining data integrity and preventing accidental deletion of non-existent entities.

| Category        | Rule Name                         | Description                                                                                                            |
| --------------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Data validation | Existing Tax Class Validation     | Only Tax Classes that exist in the database and are managed by the system can be considered for removal.               |
| Data validation | Unique Identification Requirement | The Tax Class to be removed must be identified by its unique ID, ensuring the correct entity is targeted for deletion. |

<SwmSnippet path="/sm-core/src/main/java/com/salesmanager/core/business/services/tax/TaxClassServiceImpl.java" line="45">

---

In `delete`, we kick things off by grabbing the TaxClass from the DB using its ID. This makes sure we're working with the actual entity managed by JPA, which is needed before we can delete it. Next, we call getById to get that entity.

```java
	public void delete(TaxClass taxClass) throws ServiceException {
		
		TaxClass t = getById(taxClass.getId());
```

---

</SwmSnippet>

## Entity Retrieval by ID

The main product role of this section is to ensure that when a user or system requests an entity by its ID, the system reliably returns the entity if it exists, or provides a clear error if it does not. This prevents operations on non-existent entities and maintains data integrity.

| Category        | Rule Name                        | Description                                                                                                                                   |
| --------------- | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Data validation | Entity existence verification    | When retrieving an entity by its ID, the system must verify that the entity exists before allowing any further actions to be performed on it. |
| Business logic  | Operation on valid entities only | The system must only allow operations (such as delete or update) on entities that have been successfully retrieved and verified to exist.     |

<SwmSnippet path="/sm-core/src/main/java/com/salesmanager/core/business/services/tax/TaxClassServiceImpl.java" line="53">

---

The previous attempt was a bit much, so here’s the gist - `getById` grabs the TaxClass from the repo, making sure we have something to work with before deleting.

```java
	public TaxClass getById(Long id) {
		return taxClassRepository.getOne(id);
	}
```

---

</SwmSnippet>

<SwmSnippet path="/sm-shop/src/main/java/com/salesmanager/shop/store/facade/category/CategoryFacadeImpl.java" line="365">

---

`getOne` tries to fetch a Category by its ID and store. If it doesn't find one, it throws a ResourceNotFoundException, making sure we don't proceed with a missing entity. This mirrors the entity existence check in TaxClassServiceImpl.getById.

```java
	private Category getOne(Long categoryId, int storeId) {
		return Optional.ofNullable(categoryService.getById(categoryId)).orElseThrow(
				() -> new ResourceNotFoundException(String.format("No Category found for ID : %s", categoryId)));
	}
```

---

</SwmSnippet>

## Performing the Entity Deletion

<SwmSnippet path="/sm-core/src/main/java/com/salesmanager/core/business/services/tax/TaxClassServiceImpl.java" line="48">

---

Back in TaxClassServiceImpl.delete, after getting the managed TaxClass entity, we pass it to super.delete to actually remove it from the database. This wraps up the removal process.

```java
		super.delete(t);
		
	}
```

---

</SwmSnippet>

&nbsp;

*This is an auto-generated document by Swimm 🌊 and has not yet been verified by a human*

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBc2hvcGl6ZXIlM0ElM0FTd2ltbS1EZW1v" repo-name="shopizer"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
