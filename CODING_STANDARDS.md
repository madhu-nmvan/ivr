# Coding standards — Python (FastAPI) + React (JavaScript)

## Fail with an exception, not an error return

A service that cannot continue raises. An API client that fails throws. Neither returns an ad-hoc error shape for callers to unpack.

```python
if course is None:
    raise CourseNotFound(course_id)
```

```js
if (!response.ok) throw new Error(`HTTP ${response.status}`);
```

Map domain exceptions to HTTP errors at the FastAPI boundary; show request failures in the React error state.

## Parse untrusted input with a schema, at the edge

Use **Pydantic** for FastAPI input and **Zod** for React forms, URLs, and external responses. Parse before calling services; do not scatter checks through business logic. Reject empty IDs instead of coercing them to zero.

```python
class LessonInput(BaseModel):
    lesson_id: int = Field(gt=0)
```

```js
const LessonInput = z.object({
  lessonId: z.coerce.number().int().positive(),
});
```

For HTML forms, reject `""` before numeric coercion: `z.coerce.number()` alone accepts it as zero.

## Check permission in every backend operation

A React route guard only changes what users see; it cannot authorize an API request. Check permissions for each protected read and write in FastAPI, including POST actions.

```python
if not actor.can_approve_refund:
    raise PermissionDenied()
```

## Routes and components do not own data access

FastAPI endpoints call feature services; services enforce rules and use repositories. React components use feature API functions or hooks rather than embedding unrelated fetch logic. Keep each feature's rules behind one interface.

```python
@router.get('/courses/{course_id}')
def read_course(course_id: int, service: CourseServiceDep):
    return service.get(course_id)
```

```js
// features/courses/api.js
export const getCourse = (id) => api.get(`/courses/${id}`);
```

## Arrange tests through the module under test

Set up service tests through the service's public operations, not direct table inserts. Database integration tests are the exception. Test React components through user interactions, not implementation details.

```python
course = course_service.create(title='Algebra')
assert course_service.get(course.id).title == 'Algebra'
```

## A split forced by the token limit is in scope

A touched file exceeding **5,000 tokens** must be split by responsibility. Treat the split as a move: extracted behavior stays unchanged, tests still pass, and new files follow these standards. Enforce with a repository pre-commit script; the limit does not enforce itself.

## Wrap related writes in a transaction

If two writes must succeed together, commit neither until both succeed. A refund must not be approved if its enrollment cancellation fails.

```python
with session.begin():
    refund.status = RefundStatus.APPROVED
    enrollment.active = False
```

A DB transaction cannot roll back an external payment or message; use idempotency and an outbox where needed.

## Do not query the database in a loop

One query per item is an N+1 problem. Fetch related records in bulk or use eager loading.

```python
# Bad
notes = [repo.notes_for(lesson.id) for lesson in lessons]

# Good
notes = repo.notes_for_lessons([lesson.id for lesson in lessons])
```

## Paginate anything that can grow

Enforce bounded page sizes and deterministic ordering. Never fetch all orders, students, or lessons merely to filter them in React.

```python
limit: int = Query(default=50, ge=1, le=100)
```

Use cursor pagination when large or frequently changing collections make offsets unreliable.

## Soft-delete anything people or records reference

Preserve linked lessons, orders, and other referenced entities. Set `deleted_at`, filter them from normal reads, and define retention/hard-deletion exceptions explicitly.

```python
course.deleted_at = now
```

## Enumerations live in one domain definition

Do not repeat business status strings. Define them once in the Python domain model and expose a documented API contract to JavaScript.

```python
class RefundStatus(StrEnum):
    PENDING = 'pending'
    APPROVED = 'approved'
```

## Inject the clock

Do not call `datetime.now()` inside time-dependent business rules. Accept an aware UTC timestamp so tests can cover exact boundaries.

```python
def can_refund(purchased_at: datetime, now: datetime) -> bool:
    return now - purchased_at <= timedelta(days=30)
```

## Keep secrets on the server

Read credentials in FastAPI server configuration, never in React bundles or browser storage. `VITE_*` variables and shipped JavaScript are public. Never log tokens.

## Do not put side effects in the render path

React rendering must be pure. Start requests through a loader, query hook, event handler, or correctly dependent effect. Cancel or ignore stale requests when an ID changes.

```jsx
useEffect(() => {
  const controller = new AbortController();
  loadCourse(courseId, { signal: controller.signal });
  return () => controller.abort();
}, [courseId]);
```

## Key controlled fields by the record they edit

A draft must not carry from course A to course B. Key editor instances by stable record IDs, and use stable IDs for reorderable lists.

```jsx
return <LessonNote key={lesson.id} note={note} />;
```

## Client and server share one normalization contract

Python and JavaScript cannot import the same function. Specify trimming, casing, and length rules in one API contract and test both implementations against the same examples. Otherwise a saved value can appear perpetually modified.

## Derive, do not store

Do not maintain independent state for values computed from existing state.

```jsx
// Bad
const [count, setCount] = useState(0);

// Good
const count = items.length;
```

Do not mirror fetched server data in additional local state without a concrete editing need.

## Make retries safe and errors visible

Do not swallow Python exceptions. Show loading, empty, and failure states in React; disable pending submit buttons. For refunds and payments, enforce idempotency on the backend—disabling a button is not enough.

## A decision lives in an ADR, not a comment

Put architecture and product decisions in `docs/adr/`. Comments may explain unusual technical constraints, not permanent policy. When a diff changes a decision, update or supersede its ADR.

## Enforce the rules

**Python packages:** `fastapi`, `pydantic`, `sqlalchemy`, `pytest`, `ruff`, `pyright`.

**React packages:** `react`, `zod`, `oxlint`, `prettier`, `vitest`, `@testing-library/react` (plus the router/query library actually used).

**Pre-commit:** Ruff, Oxlint, Prettier, secrets check, 5,000-token file check on touched files.

**CI:** Pyright, pytest, Vitest, integration tests, import-boundary checks, and critical Playwright flows. Automate rules rather than assuming a written convention is enforced.
