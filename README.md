# EntityFrameworkPaginateCore
EF Pagination for NetStandard and .Net Core with full async support

add nuget to project

```
Install-Package EntityFrameworkPaginateCore
```

add

```
using EntityFrameworkPaginateCore;
```

to you provider

Has 1 method and 2 overloads for that method overloads allow sorting and filtering. use the sort object and the filter objects

```
public async Task<Page<Example>> GetPaginatedExample(
            int pageSize = 10, 
            int currentPage = 1, 
            string searchText = "", 
            int sortBy = 2
            )
        {
            var filters = new Filters<Example>();
                filters.Add(!string.IsNullOrEmpty(searchText), x => x.Title.Contains(searchText));

            var sorts = new Sorts<Example>();
            sorts.Add(sortBy == 1, x => x.ExampleId);
            sorts.Add(sortBy == 2, x => x.Edited);
            sorts.Add(sortBy == 3, x => x.Title);

            try
            {
                return await _Context.EfExample.Select(e => _mapper.Map<Example>(e)).PaginateAsync(currentPage, pageSize, sorts, filters);
            }
            catch (Exception ex)
            {
                throw new KeyNotFoundException(ex.Message);
            }
        }
```

in your controller

```
 [Route("/example")]
        [HttpGet]
        public async Task<IActionResult> GetExample()
        {
            return Json(await _sqlDataProvider.GetPaginatedExample());
        }
```

BAM! paginated output with sorting and filtering you can exclude sorts and filters as there is an overload for each
