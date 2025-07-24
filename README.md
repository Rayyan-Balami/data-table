# Server-Side Data Table for shadcn/ui

A comprehensive, production-ready server-side data table implementation built with React, TanStack Table, and shadcn/ui components. This data table provides advanced features like server-side pagination, sorting, filtering, searching, and bulk actions with a modern, accessible UI.

## ✨ Features

- 🚀 **Server-side pagination** - Handle large datasets efficiently
- 🔍 **Advanced search** - Multi-column search with debounced input
- 🎛️ **Flexible filtering** - Support for select filters and date range filters
- 📊 **Column sorting** - Server-side sorting with visual indicators
- 👁️ **Column visibility** - Show/hide columns dynamically
- 📌 **Column pinning** - Pin select and action columns to left/right
- ✅ **Row selection** - Multi-row selection with bulk actions
- 🎨 **Modern UI** - Built with shadcn/ui components
- ♿ **Accessible** - Keyboard navigation and screen reader support
- 📱 **Responsive** - Works on all device sizes
- ⚡ **Performance optimized** - Debounced inputs and efficient rendering

## 🏗️ Architecture

### Core Component: `DataTableServerSide`

The main component that orchestrates all data table functionality. It handles:
- Table rendering with TanStack Table
- Server-side state management
- Integration with all sub-components

### Component Breakdown

#### `DataTableServerSide.tsx`
- **Purpose**: Main data table component with server-side capabilities
- **Features**: 
  - Server-side pagination, sorting, and filtering
  - Row selection and bulk actions
  - Column pinning (select column left, actions column right)
  - Loading states and error handling
  - Responsive design with horizontal scrolling

#### `DataTableSearch.tsx`
- **Purpose**: Advanced search functionality
- **Features**:
  - Multi-column search configuration
  - Global search across specified columns
  - Individual column search filters
  - Debounced input to prevent excessive API calls
  - Search column selection dropdown

#### `DataTableFilters.tsx`
- **Purpose**: Advanced filtering system
- **Features**:
  - Select-based filters with multiple options
  - Date range filters
  - Debounced filter changes
  - Filter state synchronization
  - Clear filters functionality

#### `DataTableColumnHeader.tsx`
- **Purpose**: Sortable column headers
- **Features**:
  - Visual sorting indicators (asc/desc/none)
  - Dropdown menu for sorting options
  - Column hide/show functionality
  - Accessible keyboard navigation

#### `DataTablePagination.tsx`
- **Purpose**: Server-side pagination controls
- **Features**:
  - First, previous, next, last page navigation
  - Page size selection
  - Row selection count display
  - Responsive layout

#### `DataTableViewOptions.tsx`
- **Purpose**: Column visibility management
- **Features**:
  - Toggle column visibility
  - Dropdown menu with all available columns
  - Checkbox-based selection

#### `DataTableActionDialog.tsx`
- **Purpose**: Bulk action operations
- **Features**:
  - Confirmation dialogs for bulk actions
  - Row selection validation
  - Loading states during actions
  - Customizable action buttons

## 🚀 Quick Start

### Installation

```bash
npm install @tanstack/react-table lucide-react
```

### Dependencies

Ensure you have the following shadcn/ui components installed:

```bash
npx shadcn-ui@latest add table button input dropdown-menu select combobox
```

### Basic Usage

```tsx
import { DataTableServerSide } from "@/components/data-table/DataTableServerSide";
import { ColumnDef } from "@tanstack/react-table";

interface User {
  id: string;
  name: string;
  email: string;
  status: string;
}

const columns: ColumnDef<User>[] = [
  {
    id: "select",
    header: ({ table }) => (
      <Checkbox
        checked={table.getIsAllPageRowsSelected()}
        onCheckedChange={(value) => table.toggleAllPageRowsSelected(!!value)}
      />
    ),
    cell: ({ row }) => (
      <Checkbox
        checked={row.getIsSelected()}
        onCheckedChange={(value) => row.toggleSelected(!!value)}
      />
    ),
  },
  {
    accessorKey: "name",
    header: ({ column }) => (
      <DataTableColumnHeader column={column} title="Name" />
    ),
  },
  {
    accessorKey: "email",
    header: ({ column }) => (
      <DataTableColumnHeader column={column} title="Email" />
    ),
  },
  {
    id: "actions",
    cell: ({ row }) => (
      <Button variant="ghost" size="sm">
        Edit
      </Button>
    ),
  },
];

export function UsersTable() {
  const [data, setData] = useState<User[]>([]);
  const [pagination, setPagination] = useState({
    page: 1,
    pageSize: 10,
    totalItems: 0,
    totalPages: 0,
    hasNextPage: false,
    hasPreviousPage: false,
  });
  const [loading, setLoading] = useState(false);

  const handlePaginationChange = (newPagination) => {
    // Fetch data with new pagination
    fetchData(newPagination);
  };

  const handleSortingChange = (sorting) => {
    // Fetch data with new sorting
    fetchDataWithSorting(sorting);
  };

  return (
    <DataTableServerSide
      columns={columns}
      data={data}
      pagination={pagination}
      loading={loading}
      onPaginationChange={handlePaginationChange}
      onSortingChange={handleSortingChange}
      searchColumns={[
        { column: "name", label: "Name" },
        { column: "email", label: "Email" },
      ]}
      filters={[
        {
          column: "status",
          label: "Status",
          options: [
            { label: "Active", value: "active" },
            { label: "Inactive", value: "inactive" },
          ],
        },
      ]}
      actionDialogs={{
        delete: {
          title: "Delete Users",
          description: "Are you sure you want to delete the selected users?",
          trigger: <Button variant="destructive">Delete Selected</Button>,
          confirmButtonText: "Delete",
          onConfirm: async (selectedIds) => {
            // Handle bulk delete
            await deleteUsers(selectedIds);
          },
        },
      }}
    />
  );
}
```

## 📋 API Reference

### DataTableServerSide Props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `columns` | `ColumnDef<TData, TValue>[]` | ✅ | Table column definitions |
| `data` | `TData[]` | ✅ | Table data array |
| `pagination` | `ServerSidePagination` | ✅ | Pagination state and metadata |
| `loading` | `boolean` | ❌ | Loading state indicator |
| `filters` | `FilterOption[]` | ❌ | Available filter options |
| `dateRangeFilters` | `DateRangeFilterOption[]` | ❌ | Date range filter options |
| `searchColumns` | `SearchColumn[]` | ❌ | Searchable column definitions |
| `onPaginationChange` | `(pagination: PaginationState) => void` | ✅ | Pagination change handler |
| `onSortingChange` | `(sorting: SortingState) => void` | ❌ | Sorting change handler |
| `onSearchChange` | `(search: string, columns?: string[]) => void` | ❌ | Search change handler |
| `onFiltersChange` | `(filters: Record<string, any>) => void` | ❌ | Filter change handler |
| `actionDialogs` | `ActionDialogConfig` | ❌ | Bulk action dialog configurations |

### Type Definitions

```typescript
interface ServerSidePagination {
  page: number;
  pageSize: number;
  totalItems: number;
  totalPages: number;
  hasNextPage: boolean;
  hasPreviousPage: boolean;
}

interface FilterOption {
  column: string;
  label: string;
  options: { label: string; value: string }[];
}

interface DateRangeFilterOption {
  column: string;
  label: string;
}

interface SearchColumn {
  column: string;
  label: string;
}
```

## 🎨 Styling & Customization

The components use shadcn/ui styling by default. You can customize the appearance by:

1. **Modifying CSS classes**: Update the className props in components
2. **Theming**: Use shadcn/ui theming system
3. **Custom components**: Replace any sub-component with your own implementation

## 🔧 Advanced Features

### Column Pinning
- Select column is automatically pinned to the left
- Actions column is automatically pinned to the right
- Maintains visibility during horizontal scrolling

### Debounced Inputs
- Search inputs are debounced to prevent excessive API calls
- Filter changes are debounced for optimal performance
- Default debounce delay: 300ms

### Row Selection
- Multi-row selection with checkbox column
- Bulk actions on selected rows
- Selection state persistence across pagination

### Responsive Design
- Horizontal scrolling for narrow screens
- Responsive pagination controls
- Adaptive filter and search layouts

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📄 License

MIT License - feel free to use in your projects!

---

Built with ❤️ using [TanStack Table](https://tanstack.com/table) and [shadcn/ui](https://ui.shadcn.com/)