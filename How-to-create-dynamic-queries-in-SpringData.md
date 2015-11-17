Compared to iBatis, Spring Data does quite easily support dynamic queries. You should consider the following when creating a dynamic query.

First let’s assume there is a PerfTest class.

```
/**
 * Peformance Test Entity
 *
 */
@Entity
@Table(name = "PERF_TEST")
public class PerfTest extends BaseModel<PerfTest> {
 
    /**
     * UUID
     */
    private static final long serialVersionUID = 1369809450686098944L;
 
    @Column(name = "name")
    private String name;
 
    @Column(length = 2048)
    private String description;
 
    @Enumerated(EnumType.STRING)
    private Status status = Status.READY;
...
}
```

Now I want to query PerfTest list which has the property status of FINISHED, ENDED..

In SQL, It might be “select * from perf_test where status in (‘FINISHED’, ‘ENDED’).

To make it very flexible, we can add JpaSpecificationExecutor<PerfTest> into PerfTestRepository.

```
import java.util.List;
 
import org.ngrinder.perftest.model.PerfTest;
import org.ngrinder.perftest.model.Status;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;
 
 
public interface PerfTestRepository extends JpaRepository<PerfTest, Integer>, JpaSpecificationExecutor<PerfTest> {
    List<PerfTest> findAllByStatusOrderByCreateDateAsc(Status status);
 
}
```

This will add several JpaSpecificationExecutor  methods into PerfTestRepository..

```
/**
     * Returns a single entity matching the given {@link Specification}.
     *
     * @param spec
     * @return
     */
    T findOne(Specification<T> spec);
 
    /**
     * Returns all entities matching the given {@link Specification}.
     *
     * @param spec
     * @return
     */
    List<T> findAll(Specification<T> spec);
 
    /**
     * Returns a {@link Page} of entities matching the given {@link Specification}.
     *
     * @param spec
     * @param pageable
     * @return
     */
    Page<T> findAll(Specification<T> spec, Pageable pageable);
 
    /**
     * Returns all entities matching the given {@link Specification} and {@link Sort}.
     *
     * @param spec
     * @param sort
     * @return
     */
    List<T> findAll(Specification<T> spec, Sort sort);
 
    /**
     * Returns the number of instances that the given {@link Specification} will return.
     *
     * @param spec the {@link Specification} to count instances for
     * @return the number of instances
     */
    long count(Specification<T> spec);
```

These methods dynamically generate SQL based on specs that you pass as argument.

To make specs, You may need a utility method.. I think it’s best to place the utility method in the model class, as follows..

```
/**
 * Peformance Test Entity
 *
 */
@Entity
@Table(name = "PERF_TEST")
public class PerfTest extends BaseModel<PerfTest> {
      ....
 
    public static Specification<PerfTest> statusSetEqual(final Status... statuses) {
        return new Specification<PerfTest>() {
            @Override
             
            public Predicate toPredicate(Root<PerfTest> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                return cb.not(root.get("status").in((Object[]) statuses));
            }
        };
 
    }
}
```

This statusSetEqual method generates a Specification instance that generates "in" clauses.

The test code is as follows.

```
public class PerfTestRepositoryTest extends NGrinderIocTransactionalTestBase {
 
    @Autowired
    public PerfTestRepository repo;
 
    @Before
    public void before() {
        PerfTest entity = new PerfTest();
        entity.setStatus(Status.FINISHED);
        repo.save(entity);
        PerfTest entity2 = new PerfTest();
        entity2.setStatus(Status.CANCELED);
        repo.save(entity2);
        PerfTest entity3 = new PerfTest();
        entity3.setStatus(Status.READY);
        repo.save(entity3);
    }
 
    @Test
    public void testPerfTest() {
        List<PerfTest> findAll = repo.findAll(PerfTest.statusSetEqual(Status.FINISHED, Status.CANCELED));
        assertThat(findAll.size(), is(2));
    }
}
```