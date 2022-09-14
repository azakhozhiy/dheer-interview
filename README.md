```php
<?php

/*
 * You have several files:
 * - routes.php
 * - Department model
 * - Employee model
 * - Employee Repository Interface
 * - Employee Repository
 * - Custom Service Provider
 * - EmployeeController
 * Find all the problems in the code comments and solve them.
*/

// routes.php
Route::get('/departments/{id}/employees', [EmployeeController::class, 'getByDepartment']);

// The department has no employee relations
class Department extends Model{
    protected $table = 'departments';
}

// Employees have a relationship with the department
class Employee extends Model{
    protected $table = 'employees';
    
    public function department(): BelongsTo{
        return $this->belongsTo(Deparment::class, 'department_id');
    }
}

interface EmployeeRepositoryInterface{
    public function getManyByDepartment(): Collection;
}

class EmployeeRepository implements EmployeeRepositoryInterface{
    public function query(): Builder{
        return Employee::query();
    }
    
    // 3. Third issue. How do you get all the employees by the department?
    public function getManyByDepartment(Department $department): Collection {
       // some code...
    }
}

class CustomServiceProvider extends ServiceProvider{
    public function register(): void{
        // some code...
    }
    
    public function boot(): void{
        // some code
    }
}

class EmployeeController extends Controller{
    // 1. First issue. Service Container cannot reach EmployeeRepositoryInterface. 
    // What do we need to do to get EmployeeRepository through EmployeeRepositoryInterface?
    // Service Container error: Target EmployeeRepositoryInterface is not instantiable
    public function getByDepartment(
        Request $request, 
        int $departmentId, 
        EmployeeRepositoryInterface $employeeRepository
    ): JsonResponse{
        
        // 2. Second issue. How do you get a department by id?
        $department = Department::query();
       
        return response()->json($employeeRepository->getManyByDepartment($department));
    }
}

?>
```
