from pptx import Presentation
from pptx.util import Inches, Pt
from pptx.dml.color import RGBColor
from pptx.enum.text import PP_ALIGN, MSO_ANCHOR
from pptx.enum.shapes import MSO_SHAPE_TYPE
from pptx.enum.dml import MSO_THEME_COLOR
import json

class PowerPointEditor:
    def __init__(self, file_path=None):
        """Initialize with existing presentation or create new one"""
        if file_path:
            self.prs = Presentation(file_path)
        else:
            self.prs = Presentation()
        self.presentation_data = {}
    
    def read_complete_presentation(self):
        """Read all presentation data including formatting"""
        self.presentation_data = {
            'slide_width': self.prs.slide_width,
            'slide_height': self.prs.slide_height,
            'slides': []
        }
        
        for slide_num, slide in enumerate(self.prs.slides):
            slide_data = {
                'slide_number': slide_num + 1,
                'layout_name': slide.slide_layout.name,
                'background': self._get_slide_background(slide),
                'shapes': []
            }
            
            for shape_idx, shape in enumerate(slide.shapes):
                shape_data = self._extract_shape_data(shape, shape_idx)
                slide_data['shapes'].append(shape_data)
            
            self.presentation_data['slides'].append(slide_data)
        
        return self.presentation_data
    
    def _get_slide_background(self, slide):
        """Extract slide background information"""
        background_info = {
            'type': 'default',
            'color': None,
            'image': None
        }
        
        try:
            if slide.background.fill.type == 1:  # Solid fill
                background_info['type'] = 'solid'
                if hasattr(slide.background.fill, 'fore_color'):
                    background_info['color'] = self._get_color_info(slide.background.fill.fore_color)
        except:
            pass  # Use default if background info not accessible
        
        return background_info
    
    def _extract_shape_data(self, shape, shape_idx):
        """Extract complete shape data including formatting"""
        shape_data = {
            'shape_index': shape_idx,
            'shape_type': shape.shape_type,
            'name': shape.name,
            'position': {
                'left': shape.left,
                'top': shape.top,
                'width': shape.width,
                'height': shape.height
            },
            'rotation': getattr(shape, 'rotation', 0),
            'text_content': None,
            'fill': self._get_fill_info(shape),
            'line': self._get_line_info(shape)
        }
        
        # Extract text and formatting if shape has text
        if hasattr(shape, 'text_frame') and shape.text_frame:
            shape_data['text_content'] = self._extract_text_formatting(shape.text_frame)
        elif hasattr(shape, 'text') and shape.text:
            shape_data['text_content'] = {'text': shape.text, 'paragraphs': []}
        
        # Handle specific shape types
        if shape.shape_type == MSO_SHAPE_TYPE.TABLE:
            shape_data['table_data'] = self._extract_table_formatting(shape.table)
        elif shape.shape_type == MSO_SHAPE_TYPE.PICTURE:
            shape_data['image_data'] = self._extract_image_info(shape)
        
        return shape_data
    
    def _extract_text_formatting(self, text_frame):
        """Extract detailed text formatting information"""
        text_data = {
            'text': text_frame.text,
            'margin_left': text_frame.margin_left,
            'margin_top': text_frame.margin_top,
            'margin_right': text_frame.margin_right,
            'margin_bottom': text_frame.margin_bottom,
            'word_wrap': text_frame.word_wrap,
            'auto_size': text_frame.auto_size,
            'vertical_anchor': getattr(text_frame, 'vertical_anchor', None),
            'paragraphs': []
        }
        
        for para_idx, paragraph in enumerate(text_frame.paragraphs):
            para_data = {
                'paragraph_index': para_idx,
                'text': paragraph.text,
                'alignment': paragraph.alignment,
                'level': paragraph.level,
                'space_before': getattr(paragraph, 'space_before', None),
                'space_after': getattr(paragraph, 'space_after', None),
                'line_spacing': getattr(paragraph, 'line_spacing', None),
                'runs': []
            }
            
            for run_idx, run in enumerate(paragraph.runs):
                run_data = {
                    'run_index': run_idx,
                    'text': run.text,
                    'font': self._extract_font_info(run.font)
                }
                para_data['runs'].append(run_data)
            
            text_data['paragraphs'].append(para_data)
        
        return text_data
    
    def _extract_font_info(self, font):
        """Extract font formatting information"""
        font_data = {
            'name': font.name,
            'size': font.size.pt if font.size else None,
            'bold': font.bold,
            'italic': font.italic,
            'underline': font.underline,
            'color': self._get_color_info(font.color) if font.color else None,
            'language_id': getattr(font, 'language_id', None)
        }
        return font_data
    
    def _get_color_info(self, color_obj):
        """Extract color information"""
        try:
            if hasattr(color_obj, 'rgb'):
                return {
                    'type': 'rgb',
                    'rgb': (color_obj.rgb.r, color_obj.rgb.g, color_obj.rgb.b)
                }
            elif hasattr(color_obj, 'theme_color'):
                return {
                    'type': 'theme',
                    'theme_color': color_obj.theme_color
                }
        except:
            pass
        return None
    
    def _get_fill_info(self, shape):
        """Extract shape fill information"""
        try:
            if hasattr(shape, 'fill'):
                fill_info = {
                    'type': shape.fill.type,
                    'solid_color': None,
                    'gradient': None,
                    'pattern': None
                }
                
                if shape.fill.type == 1:  # Solid fill
                    fill_info['solid_color'] = self._get_color_info(shape.fill.fore_color)
                
                return fill_info
        except:
            pass
        return None
    
    def _get_line_info(self, shape):
        """Extract shape line/border information"""
        try:
            if hasattr(shape, 'line'):
                line_info = {
                    'color': self._get_color_info(shape.line.color) if shape.line.color else None,
                    'width': shape.line.width,
                    'dash_style': getattr(shape.line, 'dash_style', None)
                }
                return line_info
        except:
            pass
        return None
    
    def _extract_table_formatting(self, table):
        """Extract table formatting information"""
        table_data = {
            'rows': len(table.rows),
            'columns': len(table.columns),
            'data': []
        }
        
        for row_idx, row in enumerate(table.rows):
            row_data = []
            for cell_idx, cell in enumerate(row.cells):
                cell_data = {
                    'text': cell.text,
                    'text_formatting': self._extract_text_formatting(cell.text_frame) if cell.text_frame else None,
                    'fill': self._get_fill_info(cell),
                    'margin_left': cell.margin_left,
                    'margin_top': cell.margin_top,
                    'margin_right': cell.margin_right,
                    'margin_bottom': cell.margin_bottom
                }
                row_data.append(cell_data)
            table_data['data'].append(row_data)
        
        return table_data
    
    def _extract_image_info(self, shape):
        """Extract image information"""
        return {
            'name': shape.name,
            'crop_left': getattr(shape, 'crop_left', 0),
            'crop_top': getattr(shape, 'crop_top', 0),
            'crop_right': getattr(shape, 'crop_right', 0),
            'crop_bottom': getattr(shape, 'crop_bottom', 0)
        }
    
    def modify_text_formatting(self, slide_idx, shape_idx, new_formatting):
        """Modify text formatting for a specific shape"""
        slide = self.prs.slides[slide_idx]
        shape = slide.shapes[shape_idx]
        
        if hasattr(shape, 'text_frame'):
            text_frame = shape.text_frame
            
            # Apply text frame formatting
            if 'margin_left' in new_formatting:
                text_frame.margin_left = Inches(new_formatting['margin_left'])
            if 'margin_top' in new_formatting:
                text_frame.margin_top = Inches(new_formatting['margin_top'])
            if 'margin_right' in new_formatting:
                text_frame.margin_right = Inches(new_formatting['margin_right'])
            if 'margin_bottom' in new_formatting:
                text_frame.margin_bottom = Inches(new_formatting['margin_bottom'])
            
            # Apply paragraph and run formatting
            for para_idx, paragraph in enumerate(text_frame.paragraphs):
                if 'paragraphs' in new_formatting and para_idx < len(new_formatting['paragraphs']):
                    para_format = new_formatting['paragraphs'][para_idx]
                    
                    if 'alignment' in para_format:
                        paragraph.alignment = para_format['alignment']
                    if 'level' in para_format:
                        paragraph.level = para_format['level']
                    
                    # Apply run formatting
                    for run_idx, run in enumerate(paragraph.runs):
                        if 'runs' in para_format and run_idx < len(para_format['runs']):
                            run_format = para_format['runs'][run_idx]
                            
                            if 'font' in run_format:
                                self._apply_font_formatting(run.font, run_format['font'])
    
    def _apply_font_formatting(self, font, font_format):
        """Apply font formatting"""
        if 'name' in font_format and font_format['name']:
            font.name = font_format['name']
        if 'size' in font_format and font_format['size']:
            font.size = Pt(font_format['size'])
        if 'bold' in font_format:
            font.bold = font_format['bold']
        if 'italic' in font_format:
            font.italic = font_format['italic']
        if 'underline' in font_format:
            font.underline = font_format['underline']
        if 'color' in font_format and font_format['color']:
            if font_format['color']['type'] == 'rgb':
                rgb = font_format['color']['rgb']
                font.color.rgb = RGBColor(rgb[0], rgb[1], rgb[2])
    
    def modify_shape_properties(self, slide_idx, shape_idx, properties):
        """Modify shape properties like position, size, fill, etc."""
        slide = self.prs.slides[slide_idx]
        shape = slide.shapes[shape_idx]
        
        # Position and size
        if 'left' in properties:
            shape.left = Inches(properties['left'])
        if 'top' in properties:
            shape.top = Inches(properties['top'])
        if 'width' in properties:
            shape.width = Inches(properties['width'])
        if 'height' in properties:
            shape.height = Inches(properties['height'])
        if 'rotation' in properties:
            shape.rotation = properties['rotation']
        
        # Fill properties
        if 'fill_color' in properties:
            color = properties['fill_color']
            if color['type'] == 'rgb':
                shape.fill.solid()
                shape.fill.fore_color.rgb = RGBColor(color['rgb'][0], color['rgb'][1], color['rgb'][2])
        
        # Line properties
        if 'line_color' in properties:
            color = properties['line_color']
            if color['type'] == 'rgb':
                shape.line.color.rgb = RGBColor(color['rgb'][0], color['rgb'][1], color['rgb'][2])
        if 'line_width' in properties:
            shape.line.width = Pt(properties['line_width'])
    
    def add_text_box(self, slide_idx, left, top, width, height, text, formatting=None):
        """Add a new text box with specified formatting"""
        slide = self.prs.slides[slide_idx]
        
        # Add text box
        text_box = slide.shapes.add_textbox(
            Inches(left), Inches(top), Inches(width), Inches(height)
        )
        
        # Add text
        text_frame = text_box.text_frame
        text_frame.text = text
        
        # Apply formatting if provided
        if formatting:
            if 'font_name' in formatting:
                text_frame.paragraphs[0].runs[0].font.name = formatting['font_name']
            if 'font_size' in formatting:
                text_frame.paragraphs[0].runs[0].font.size = Pt(formatting['font_size'])
            if 'font_color' in formatting:
                color = formatting['font_color']
                text_frame.paragraphs[0].runs[0].font.color.rgb = RGBColor(
                    color[0], color[1], color[2]
                )
            if 'bold' in formatting:
                text_frame.paragraphs[0].runs[0].font.bold = formatting['bold']
            if 'italic' in formatting:
                text_frame.paragraphs[0].runs[0].font.italic = formatting['italic']
    
    def save_presentation(self, output_path):
        """Save the modified presentation"""
        self.prs.save(output_path)
        print(f"Presentation saved to: {output_path}")
    
    def export_formatting_data(self, json_path):
        """Export all formatting data to JSON for backup/analysis"""
        with open(json_path, 'w') as f:
            json.dump(self.presentation_data, f, indent=2, default=str)
        print(f"Formatting data exported to: {json_path}")

# Example usage
if __name__ == "__main__":
    # Initialize editor with existing presentation
    editor = PowerPointEditor("input.pptx")
    
    # Read complete presentation data
    print("Reading presentation...")
    presentation_data = editor.read_complete_presentation()
    
    # Display current formatting information
    print(f"Presentation has {len(presentation_data['slides'])} slides")
    
    for slide_idx, slide in enumerate(presentation_data['slides']):
        print(f"\nSlide {slide['slide_number']} ({slide['layout_name']}):")
        
        for shape_idx, shape in enumerate(slide['shapes']):
            print(f"  Shape {shape_idx}: {shape['name']} (Type: {shape['shape_type']})")
            
            if shape['text_content']:
                print(f"    Text: {shape['text_content']['text'][:50]}...")
                
                # Display font information for each run
                for para in shape['text_content']['paragraphs']:
                    for run in para['runs']:
                        font = run['font']
                        print(f"      Font: {font['name']}, Size: {font['size']}, "
                              f"Bold: {font['bold']}, Color: {font['color']}")
    
    # Example modifications
    print("\nApplying modifications...")
    
    # Modify font formatting for first text shape in first slide
    if presentation_data['slides'] and presentation_data['slides'][0]['shapes']:
        new_formatting = {
            'paragraphs': [{
                'runs': [{
                    'font': {
                        'name': 'Arial',
                        'size': 24,
                        'bold': True,
                        'color': {'type': 'rgb', 'rgb': (255, 0, 0)}  # Red color
                    }
                }]
            }]
        }
        editor.modify_text_formatting(0, 0, new_formatting)
    
    # Modify shape properties
    shape_properties = {
        'fill_color': {'type': 'rgb', 'rgb': (200, 200, 255)},  # Light blue
        'line_color': {'type': 'rgb', 'rgb': (0, 0, 255)},      # Blue border
        'line_width': 2
    }
    if presentation_data['slides'] and presentation_data['slides'][0]['shapes']:
        editor.modify_shape_properties(0, 0, shape_properties)
    
    # Add a new text box
    editor.add_text_box(
        slide_idx=0,
        left=1, top=1, width=4, height=1,
        text="Added by Python!",
        formatting={
            'font_name': 'Calibri',
            'font_size': 18,
            'font_color': (0, 128, 0),  # Green
            'bold': True
        }
    )
    
    # Export formatting data for analysis
    editor.export_formatting_data("formatting_backup.json")
    
    # Save the modified presentation
    editor.save_presentation("output_modified.pptx")
    
    print("Modifications complete!")
