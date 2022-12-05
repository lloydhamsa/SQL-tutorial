# SQL-tutorial

SQL group

```bash
  SELECT
    COUNT(p.productId) count,
    s.status as status
  FROM (
    select
      pe.id as productId,
      pe.statusId as productStatusId
    from product_entity pe
    where pe.action = 'product'
    ) as p
  right JOIN (
    select se.name as status, se.id as id  from status_entity se
    where se.parentId is null
    group by status
  ) s on s.id = p.productStatusId
  GROUP BY status
```

result

```bash
5	New order
1	CNC
0	Backshaping
0	Infusion
...
```

## table

product_entity

```bash
  id: number;
  name: string;
  @Column({
    type: 'enum',
    enum: ActionProduct,
    nullable: true,
    default: ActionProduct.product,
  })
  action: ActionProduct;
  @ManyToOne(() => StatusEntity, (status) => status.productStatus)
  status: StatusEntity;
  createAt: Date;
  updateAt: Date;
```

status_entity

```bash
  id: number;
  key: string;
  name: string;
  @OneToMany(() => ProductEntity, (product) => product.status, {
    cascade: true,
  })
  productStatus: ProductEntity;

  @OneToMany(() => StatusEntity, (statusEntity) => statusEntity.parent)
  children: StatusEntity[];

  @Column({ nullable: true })
  parentId: number;

  @ManyToOne(() => StatusEntity, (statusEntity) => statusEntity.children)
  @JoinColumn({ name: 'parentId' })
  parent: StatusEntity;
```
