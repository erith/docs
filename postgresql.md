# docker-compose.yml
```yaml
services:
  postgres:
    image: postgres:15
    restart: always
    volumes:
      - ./postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: postgres
```

# execute docker compose
```bash
docker compose up -d
```


```sql
CREATE TABLE "tb_user" (
    seq SERIAL PRIMARY KEY, -- 유저 고유 번호
    name VARCHAR(20) NOT NULL, -- 유저 이름
    hobby VARCHAR(40) -- 유저 취미
);
COMMENT ON TABLE "tb_user" IS '유저정보';

INSERT INTO "tb_user" (name, hobby)
SELECT
    first_names.name || ' ' || last_names.name,
    'Hobby' || num
FROM
    (
        SELECT
            name,
            ROW_NUMBER() OVER () AS row_num
        FROM
            unnest(ARRAY['John', 'Jane', 'Michael', 'Emily', 'William']) AS name
    ) AS first_names
    JOIN
    (
        SELECT
            name,
            ROW_NUMBER() OVER () AS row_num
        FROM
            unnest(ARRAY['Smith', 'Johnson', 'Williams', 'Brown', 'Jones']) AS name
    ) AS last_names
    ON
        first_names.row_num = last_names.row_num
CROSS JOIN
    generate_series(1, 100) AS num;

DROP FUNCTION get_users_by_hobby(character varying) 

SELECT * FROM get_users_by_hobby(NULL);

---
CREATE OR REPLACE FUNCTION get_users_by_hobby(hobby_param VARCHAR(40))
RETURNS TABLE(num INT, username VARCHAR(20), userhobby VARCHAR(40)) AS
$$
BEGIN
    IF hobby_param IS NULL THEN
        RETURN QUERY
        SELECT seq, name, hobby
        FROM tb_user
        WHERE hobby IS NULL;
    ELSE
        RETURN QUERY
        SELECT seq, name, hobby
        FROM tb_user
        WHERE hobby = hobby_param;
    END IF;
END;
$$
LANGUAGE plpgsql;
```

