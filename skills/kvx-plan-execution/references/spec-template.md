# KVX v1 Spec Template

This file is a lightweight template. Copy sections into the KVX spec doc and fill in concrete values.

## 1. Overview
- Purpose and scope for KVX v1
- Non-goals

## 2. Terminology
- token, block/page, slot, head, head_dim, layout
- logical block vs physical block

## 3. Versioning and Negotiation
- ABI major/minor rules (major breaks, minor additive)
- Runtime negotiation flow
- Required fields in version struct

## 4. Memory Model
### 4.1 Paged KV Layout
- tokens_per_block (page size)
- block_size, num_heads, head_dim
- K/V storage split and alignment expectations

### 4.2 Layout Enum and Strides
- Enumerate supported layouts (e.g., NHD, HND)
- Define stride semantics and contiguous dimensions
- State how layout selection is made

## 5. Page Table and Slot Mapping
- block_table: logical block index -> physical block id
- slot_mapping or ragged indices: token -> slot
- Rules for padding or invalid tokens

## 6. Metadata
- Required fields (block_size, head_dim, num_heads, dtype, layout)
- Optional fields (fp8 scales, secondary pool flags, cyclic window)
- Include a table with field name, type, size, required, semantics

## 7. API Semantics
- Initialize, create/destroy cache
- Read/write semantics for single-step decode
- Thread-safety expectations
- Ownership and lifetime rules

## 8. Error Codes
- Enumerate error codes and meanings
- Define when errors are returned

## 9. Conformance Tests
- Minimum required tests for compliance
- Layout verification, version negotiation, and ABI size checks

## 10. Optional Features
- FP8 support
- Secondary pool
- Cyclic cache windowing

## 11. Compatibility and Migration
- Backward compatibility expectations
- Adapter guidance to vLLM/TGI/TensorRT-LLM
