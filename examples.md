
# collection, nested table, forloop, methods
```sql
DECLARE
   TYPE colors_tab_t IS TABLE OF VARCHAR2(30);
   my_list colors_tab_t :=
      colors_tab_t('RED','GREEN','BLUE');
   element BINARY_INTEGER;
BEGIN
   DBMS_OUTPUT.PUT_LINE('my_list has '
      ||my_list.COUNT||' elements');
   my_list.DELETE(2); -- delete element two
   DBMS_OUTPUT.PUT_LINE('my_list has '
      ||my_list.COUNT||' elements');

   FOR element IN my_list.FIRST..my_list.LAST
   LOOP
      IF my_list.EXISTS(element)
      THEN
         DBMS_OUTPUT.PUT_LINE(my_list(element)
            || ' Prior= '||my_list.PRIOR(element)
            || ' Next= ' ||my_list.NEXT(element));
      ELSE
         DBMS_OUTPUT.PUT_LINE('Element '|| element
            ||' deleted. Prior=
               '||my_list.PRIOR(element)
            || ' Next= '||my_list.NEXT(element));
      END IF;
   END LOOP;
END;
```

# PL/SQL cursor FOR LOOP example
```sql
DECLARE
  CURSOR c_product
  IS
    SELECT 
        product_name, list_price
    FROM 
        products 
    ORDER BY 
        list_price DESC;
BEGIN
  FOR r_product IN c_product
  LOOP
    dbms_output.put_line( r_product.product_name || ': $' ||  r_product.list_price );
  END LOOP;
END;
```
